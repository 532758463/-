### jest.config.js配置文件分析
将React脚手架隐藏的依赖包弹射出来。
```
npm run eject
```
`jest.config.js`

```js
modoule.exports = {

    //代码测试覆盖率
    "collectCoverageFrom": [
        //分析.js,.jsx,.ts,.tsx文件
        "src/**/*.{js,jsx,ts,tsx}", 

        //不分析以.d.ts的文件
        "!src/**/*.d.ts"  
      ],

      //运行测试前额外需要准备什么
      "setupFiles": [ 

        //解决js测试一些兼容性问题。
        "react-app-polyfill/jsdom" 
      ],

      //当测试环境建立后，想做一些其他的事情，可以在这里进行额外的准备。
      "setupFilesAfterEnv": [], 

      //匹配某个目录下的文件并进行测试。
      "testMatch": [ 

        //__test__文件夹下的js,jsx,ts,tsx文件会被认为是测试文件
        "<rootDir>/src/**/__tests__/**/*.{js,jsx,ts,tsx}",
        "<rootDir>/src/**/*.{spec,test}.{js,jsx,ts,tsx}"
      ],

      //jest运行环境
      "testEnvironment": "jest-environment-jsdom-fourteen",

      //转化处理
      "transform": { 

        //对引入文件通过babel-jest做babel转化后使用。
        "^.+\\.(js|jsx|ts|tsx)$": "<rootDir>/node_modules/babel-jest", 
        
         //对css文件进行处理，在做前端化测试时，一般不会对样式进行测试。
        "^.+\\.css$": "<rootDir>/config/jest/cssTransform.js",   

        //除了以js|jsx|ts|tsx|css|json结尾的其他文件进行转化。
        "^(?!.*\\.(js|jsx|ts|tsx|css|json)$)": "<rootDir>/config/jest/fileTransform.js" 
      },

       //忽略某些文件的转化
      "transformIgnorePatterns": [ 
        "[/\\\\]node_modules[/\\\\].+\\.(js|jsx|ts|tsx)$",
        "^.+\\.module\\.(css|sass|scss)$"
      ],

      //当做自动化测试的时候，引入的某些模块，默认会到node_modules.如果想在其他文件夹下找，则可以写一个abc就可以了。
      "modulePaths": [],
      "moduleNameMapper": {
        "^react-native$": "react-native-web",

        //identity-obj-proxy是一个第三方模块，当我们引入某个css模块时，它会把原始的这些样式变成一个js对象。
        "^.+\\.module\\.(css|sass|scss)$": "identity-obj-proxy"
      },

      //引入某个文件时未写后缀名时，会按下面的顺序进行查找。
      "moduleFileExtensions": [
        "web.js",
        "js",
        "web.ts",
        "ts",
        "web.tsx",
        "tsx",
        "json",
        "web.jsx",
        "jsx",
        "node"
      ],

      //运行测试时的插件
      "watchPlugins": [  
        "jest-watch-typeahead/filename",
        "jest-watch-typeahead/testname"
      ]
}
```