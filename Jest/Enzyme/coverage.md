# coverage

如何生成覆盖率报告？

首先在`package.json`文件中进行配置运行命令。

```js
"scripts": {
    "coverage": "node scripts/test.js --coverage --watchAll=false"
    //"coverage":"jest coverage"
  }
```

还可以指定`coverage`报告存放的位置（文件夹）。

```js
  // Indicates whether the coverage information should be collected while executing the test
  // collectCoverage: false,

  // An array of glob patterns indicating a set of files for which coverage information should be collected
  // collectCoverageFrom: null,

  // The directory where Jest should output its coverage files
  coverageDirectory: "coverage",

  // An array of regexp pattern strings used to skip coverage collection
  // coveragePathIgnorePatterns: [
  //   "\\\\node_modules\\\\"
  // ],

  // A list of reporter names that Jest uses when writing coverage reports
  // coverageReporters: [
  //   "json",
  //   "text",
  //   "lcov",
  //   "clover"
  // ],

  // An object that configures minimum threshold enforcement for coverage results
  // coverageThreshold: null,

```

