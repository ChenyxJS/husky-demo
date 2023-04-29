# husky+commitlint+eslint集成git提交规范检查

#### 介绍

- husky：git提交时触发hooks
- commitlint：对提交的内容做规范校验husky，主要对pre-commit和commit-msg钩子对校验。



#### Husky

1、安装husky开发依赖

```bash
npm i husky -D
```

2、初始化husky配置，在根目录添加**.husky**配置文件，初始化pre-commit

```bash
npx husky-init	
```

打开.husky文件夹里的pre-commit修改代码检查的执行命令

![image-20230429213009352](http://file.chenyx.site/image/202304292130376.png)

```bash
# 这个命令根据自己package.json中的配置来
npm run lint:lint-staged
```

3、另外新增一个hooks ，commit-msg

```bash
npx husky add .husky/commit-msg 'npx --no-install commitlint --edit "$1"'
```

安装完成后的项目结构

![image-20230429201835321](http://file.chenyx.site/image/202304292018354.png)

#### Eslint+Stylelint+Prettier

> 这里的依赖和配置就根据自己的来，我这只提供个参考

1、安装Eslint

```bash
npm i -D eslint eslint-config-prettier eslint-plugin-prettier eslint-plugin-vue @typescript-eslint/eslint-plugin @typescript-eslint/parser
```

2、安装Stylelint

```bash
npm i -D stylelint stylelint-config-html stylelint-config-recess-order stylelint-config-recommended-scss stylelint-config-recommended-vue stylelint-config-standard stylelint-config-standard-scss
```

3、 安装Prettier

```bash
npm i -D prettier
```



#### Lint-staged

> [Lint-staged](https://github.com/okonet/lint-staged)可以在git staged阶段的文件上执行Linters，简单说就是当我们运行ESlint或Stylelint命令时，可以通过设置指定只检查我们通过git add添加到暂存区的文件，可以避免我们每次检查都把整个项目的代码都检查一遍，从而提高效率。 其次，Lint-staged允许指定不同类型后缀文件执行不同指令的操作，并且可以按步骤再额外执行一些其它shell指令。

1、安装Lint-staged

```bash
npm i lint-staged -D
```

2、配置Lint-staged

在package.json中添加配置

```json
 "scripts": {
    ...
    "lint:lint-staged": "lint-staged",
  },  

"lint-staged": {
    "*.{js,ts}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{cjs,json}": [
      "prettier --write"
    ],
    "*.{vue,html}": [
      "eslint --fix",
      "prettier --write",
      "stylelint --fix"
    ],
    "*.{scss,css}": [
      "stylelint --fix",
      "prettier --write"
    ],
    "*.md": [
      "prettier --write"
    ]
  }
```



#### Commitlint

1、安装commitlint

```bash
npm install @commitlint/cli @commitlint/config-conventional --save-dev
```

2、在根目录下创建commitlint.config.cjs配置文件

![image-20230429205727183](http://file.chenyx.site/image/202304292057215.png)

参考配置模版

```js
module.exports = {
  // 继承的规则
  extends: ["@commitlint/config-conventional"],
  // 自定义规则
  rules: {
    // @see https://commitlint.js.org/#/reference-rules

    // 提交类型枚举，git提交type必须是以下类型
    "type-enum": [
      2,
      "always",
      [
        "feat", // 新增功能
        "fix", // 修复缺陷
        "docs", // 文档变更
        "style", // 代码格式（不影响功能，例如空格、分号等格式修正）
        "refactor", // 代码重构（不包括 bug 修复、功能新增）
        "perf", // 性能优化
        "test", // 添加疏漏测试或已有测试改动
        "build", // 构建流程、外部依赖变更（如升级 npm 包、修改 webpack 配置等）
        "ci", // 修改 CI 配置、脚本
        "revert", // 回滚 commit
        "chore", // 对构建过程或辅助工具和库的更改（不影响源文件、测试用例）
      ],
    ],
    "subject-case": [0], // subject大小写不做校验
  },

  prompt: {
    messages: {
      type: "选择你要提交的类型 :",
      scope: "选择一个提交范围（可选）:",
      customScope: "请输入自定义的提交范围 :",
      subject: "填写简短精炼的变更描述 :\n",
      body: '填写更加详细的变更描述（可选）。使用 "|" 换行 :\n',
      breaking: '列举非兼容性重大的变更（可选）。使用 "|" 换行 :\n',
      footerPrefixesSelect: "选择关联issue前缀（可选）:",
      customFooterPrefix: "输入自定义issue前缀 :",
      footer: "列举关联issue (可选) 例如: #31, #I3244 :\n",
      generatingByAI: "正在通过 AI 生成你的提交简短描述...",
      generatedSelectByAI: "选择一个 AI 生成的简短描述:",
      confirmCommit: "是否提交或修改commit ?",
    },
    // prettier-ignore
    types: [
      { value: "feat",     name: "特性:     ✨  新增功能", emoji: ":sparkles:" },
      { value: "fix",      name: "修复:     🐛  修复缺陷", emoji: ":bug:" },
      { value: "docs",     name: "文档:     📝  文档变更", emoji: ":memo:" },
      { value: "style",    name: "格式:     💄  代码格式（不影响功能，例如空格、分号等格式修正）", emoji: ":lipstick:" },
      { value: "refactor", name: "重构:     ♻️  代码重构（不包括 bug 修复、功能新增）", emoji: ":recycle:" },
      { value: "perf",     name: "性能:     ⚡️  性能优化", emoji: ":zap:" },
      { value: "test",     name: "测试:     ✅  添加疏漏测试或已有测试改动", emoji: ":white_check_mark:"},
      { value: "build",    name: "构建:     📦️  构建流程、外部依赖变更（如升级 npm 包、修改 vite 配置等）", emoji: ":package:"},
      { value: "ci",       name: "集成:     🎡  修改 CI 配置、脚本",  emoji: ":ferris_wheel:"},
      { value: "revert",   name: "回退:     ⏪️  回滚 commit",emoji: ":rewind:"},
      { value: "chore",    name: "其他:     🔨  对构建过程或辅助工具和库的更改（不影响源文件、测试用例）", emoji: ":hammer:"},
    ],
    useEmoji: true,
    emojiAlign: "center",
    useAI: false,
    aiNumber: 1,
    themeColorCode: "",
    scopes: [],
    allowCustomScopes: true,
    allowEmptyScopes: true,
    customScopesAlign: "bottom",
    customScopesAlias: "custom",
    emptyScopesAlias: "empty",
    upperCaseSubject: false,
    markBreakingChangeMode: false,
    allowBreakingChanges: ["feat", "fix"],
    breaklineNumber: 100,
    breaklineChar: "|",
    skipQuestions: [],
    issuePrefixes: [
      { value: "closed", name: "closed:   ISSUES has been processed" },
    ],
    customIssuePrefixAlign: "top",
    emptyIssuePrefixAlias: "skip",
    customIssuePrefixAlias: "custom",
    allowCustomIssuePrefix: true,
    allowEmptyIssuePrefix: true,
    confirmColorize: true,
    maxHeaderLength: Infinity,
    maxSubjectLength: Infinity,
    minSubjectLength: 0,
    scopeOverrides: undefined,
    defaultBody: "",
    defaultIssues: "",
    defaultScope: "",
    defaultSubject: "",
  },
};

```



#### cz-git、commitizen

自定义提示插件

1、安装cz-git

```bash
npm i cz-git -D 
```

2、配置package.json

```bash
 "scripts": {
    ...
    "lint:lint-staged": "lint-staged",
    "commit": "git-cz"
  },  

"config": {
   "commitizen": {
     "path": "node_modules/cz-git"
   }
}
```

#### 使用效果

提交代码时，将git commit换成npm run commit就可以使用模版进行信息的提交了

![image-20230429224720795](http://file.chenyx.site/image/202304292247826.png)

