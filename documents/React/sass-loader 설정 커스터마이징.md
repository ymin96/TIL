# sass-loader 설정 커스터마이징

<br>

자주 사용하는 폴더를 Webpack 의 sass-loader 설정에 등록해두면 폴더 안의 파일을 import 할때  `@import '../styles/utils.scss'`처럼 모든 경로를 입력 안하고 `@import 'utils.scss'` 으로 바로 사용할 수 있게 된다.

<br>

### 설정 방법

설정 방법은 다음과 같다.

1. `yarn eject` 명령을 실행한다.
   (create-react-app으로 만든 프로젝트는 세부 설정들이 모두 숨겨져 있다. 이를 커스터마이징 하기 위해 위 명령을 입력해 세부설정을 전부 꺼내줘야 한다)
2. 프로젝트의 config -> webpack.config.js 파일을 연다.
3. sassRegex를 검색하면 

```javascript
{
	test: sassRegex,
	exclude: sassModuleRegex,
	use: getStyleLoaders({
		importLoaders: 2,
		sourceMap: isEnvProduction && shouldUseSourceMap
	}),
	sideEffects: true,
},
```

이런 코드 블록을 아래와 같이 바꿔준다.

```javascript
{
	test: sassRegex,
	exclude: sassModuleRegex,
	use: getStyleLoaders({
		importLoaders: 2,
		sourceMap: isEnvProduction && shouldUseSourceMap
	}).concat({
		loader: require.resolve('sass-loader'),
		options: {
			sourceMap: isEnvProduction && shouldUseSourceMap,
			sassOptions: {
				includePaths: [paths.appSrc + '/styles']
			}
		}
	}),
	sideEffects: true,
},
```

includePaths 안에`[paths.appSrc + '등록하고싶은 폴더 경로']`를 써주면 된다. 위의 예시의 경우 `/styles`폴더 안의 파일은 이제 경로 입력 없이 바로 import가 가능하게 된다.

<br>

### CSS Module과 같이 사용하기

이 경우는 세팅을 한가지 더 해줘야 한다. sassRegex 코드블력 바로 아래를 보면 다음과 같은 코드블록이 있다.

```javascript
{
	test: sassModuleRegex,
	use: getStyleLoaders(
		{
			importLoaders: 3,
			sourceMap: isEnvProduction && shouldUseSourceMap,
			modules: {
				getLocalIdent: getCSSModuleLocalIdent,
			},
		},
	'sass-loader'
	)
},
```

이 코드블에 위에 sassRegex때와 같은 코드를 다음과 같이 붙여주면 된다.

```javascript
{
	test: sassModuleRegex,
	use: getStyleLoaders(
		{
			importLoaders: 3,
			sourceMap: isEnvProduction && shouldUseSourceMap,
			modules: {
				getLocalIdent: getCSSModuleLocalIdent,
			},
		},
	'sass-loader'
	).concat({
		loader: require.resolve('sass-loader'),
		options: {
			sourceMap: isEnvProduction && shouldUseSourceMap,
			sassOptions: {
				includePaths: [paths.appSrc + '/styles']
			}
		}
	}),
},
```

