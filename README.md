## ESlint结合Git Hooks（以vue-cli为例）

**备注：**
```
据观察现在的vue-cli4脚手架已经集成git hook了，按照脚手架生成项目后，根目录会多出lint-staged.config配置文件，
package.json也会默认有如下配置，如果项目集成eslint,每次git commit前都会去执行pre-commit钩子，然后读取lint-staged对应的配置文件lint-staged.config执行vue-cli-service lint命令

"gitHooks": {
    "pre-commit": "lint-staged"
}

```

### 1、本地通过vue-cli初始化项目
```
<!-- 1、在某个目录通过vue-cli初始化vue项目，根据命令行提示选择eslint、babel，elint默认继承配置选择standard -->
vue create git-hooks

<!-- 2、初步演示,在项目下main.js编写如下代码，npm run serve运行就会抛出eslint错误,如果vscode安装有eslint插件，代码编写过程中也会标红提示错误 -->
const a=1//该变量如未使用，就报eslint错误

<!--  -->

```

### 2、配置git hooks
#### 2-1
```
<!-- 由于很多前端开发者并不擅长编写shell脚本，所以我们需要npm安装一个模块，Husky可以实现Git Hooks的使用需求，简单来说有了这个模块我们就可以实现不编写shell脚本也可以使用git钩子所带来的功能 -->
<!-- 注意1：下面在执行第三步时，husky会不生效，此时和husky的版本有关，例如此时把我安装的当前"husky": "^8.0.1"替换为"husky": "^4.2.5",就可以生效 -->
<!-- 注意2：通常情况下npm i husky -D会在./git/hooks目录下会额外生成一些git hooks钩子相关文件-->
<!-- 1、 -->
npm i husky -D


<!--2、在package.json下添加如下配置  -->
"scripts": {
  "lint": "vue-cli-service lint",
},

"husky": {
    "hooks": {
      "pre-commit": "npm run lint"
    }
}

<!-- 3、git提交校验 -->
git add .

//此时就会执行git hooks的pre-commit钩子，然后接着执行上面的npm run lint，此时控制台就会抛出eslint错误，导致git commit不成功
git commit -m '111'

```

#### 2-2 基于2-1husky配合lint-staged
```
<!-- 虽然husky已经可以达git commit前进行lint操作，但是有些功能还是做不到-->
<!-- 1 -->
npm i lint-staged -D

<!-- 2、在上面2-1配置的基础上新增和修改配置 -->
{
   "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
{
  "lint-staged": {//这里有多种配置方式，可查看官网
   "src/**/*.{js,vue}": [
     "npm run lint"
   ]
}
}

<!-- 3、运行 -->
git add .

//此时会在提交前执行npm run lint，lint的校验范围会根据lint-staged的配置去跑
git commit -m 'xxx'
```
lint-staged能够让lint只检测暂存区的文件,和检查指定文件...，具体可查看[lint-staged官网](https://www.npmjs.com/package/lint-staged)  [掘金博客：vue项目添加eslint+husky+lint-staged](https://juejin.cn/post/6877874860597444616#heading-3)



### 3、新建远程仓库，并推送现有的 Git 仓库

```
<!-- 1、以github为例 -->


<!-- 2 -->
git remote add origin git@github.com:Myllj/git-hooks.git

<!-- 3 -->
git add .

<!-- 4、此时如果提交前会执行git钩子，如果还有eslint错误就会抛出，需修复后再次执行git的3、4步骤 -->
git commit -m "xxx"

<!-- 5 -->
git push -u origin main
```