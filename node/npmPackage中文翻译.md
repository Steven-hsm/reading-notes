# npm的package.json处理的细节

## 描述

您需要了解所有关于package.json文件中所需内容的信息。它必须是实际的JSON，而不仅仅是JavaScript对象文字。

本文档中描述的许多行为都受到中描述的配置设置的影响`npm-config`。

## 1. name

如果您打算发布软件包，package.json中最重要就是name和version字段，因为它们是必填字段。名称和版本一起构成一个标识符，该标识符被要求完全唯一。软件包的更改和版本的更改应该同时进行。如果您不打算发布您的软件包，那么neme和version字段是可选的。

name就是包的名字。

一些规则：

- 名称必须小于或等于214个字符。这也包括了包名。
- 名称不能以点或下划线开头。
- 新软件包名称中不得包含大写字母。
- 该名称最终是URL，命令行参数和文件夹名称的一部分。因此，名称不能包含任何非URL安全的字符。

一些技巧：

- 不要使用与核心Node模块相同的名称。
- 不要在名称中添加“ js”或“ node”。由于您正在编写package.json文件，因此假设它是js，并且可以使用“ engines”字段指定引擎。（见下文）
- 该名称可能会作为参数传递给require（），因此它应该简短一些，但也应具有描述性。
- 您可能需要检查npm注册表，以查看是否已经有该名称的东西，然后再附加它。https://www.npmjs.com/

名称可以有范围的前缀，例如`@myorg/mypackage`。请参阅参考资料 `npm-scope`。

## 2. version

如果您打算发布软件包，package.json中最重要就是name和version字段，因为它们是必填字段。名称和版本一起构成一个标识符，该标识符被要求完全唯一。软件包的更改和版本的更改应该同时进行。如果您不打算发布您的软件包，那么neme和version字段是可选的。

版本必须可由[node-semver](https://github.com/isaacs/node-semver)解析 ，它与npm捆绑在一起作为依赖项。（`npm install semver`自己使用）。

有关版本号和范围的更多信息，请[参见semver](https://www.npmjs.cn/misc/semver)。

## 3. description

在其中添加描述。这是一个字符串。如中所列，这可以帮助人们发现您的包裹`npm search`。

## 4. keywords

放入关键字。它是一个字符串数组。这可以帮助人们发现您的软件包（如中所列）`npm search`。

## 5. homepage

项目首页的网址。

例：

```
"homepage": "https://github.com/owner/project#readme"
```

## 6. bugs

项目的问题跟踪器的URL或应向其报告问题的电子邮件地址。这些对于遇到您的包裹问题的人很有帮助。

它看起来应该像这样：

```
{ 
	"url" : "https://github.com/owner/project/issues", 
	"email" : "project@hostname.com"
}
```

您可以指定一个或两个值。如果您只想提供一个url，则可以将“ bugs”的值指定为简单字符串而不是对象。

如果提供了URL，则该`npm bugs`命令将使用它。

## 7. license

您应该为您的软件包指定一个许可证，以便人们知道如何使用它们，以及您对该软件包施加的任何限制。

如果您使用的是BSD-2-Clause或MIT等通用许可证，请为您使用的许可证添加当前SPDX许可证标识符，如下所示：

```
{ "license" : "BSD-3-Clause" }
```

您可以查看[SPDX许可证ID的完整列表](https://spdx.org/licenses/)。理想情况下，您应该选择经过 [OSI](https://opensource.org/licenses/alphabetical)批准的产品。

如果您的软件包已获得多个通用许可证的许可，请使用[SPDX许可证表达式语法版本2.0字符串](https://www.npmjs.com/package/spdx)，如下所示：

```
{ "license" : "(ISC OR GPL-3.0)" }
```

如果使用的许可证尚未分配SPDX标识符，或者使用的是自定义许可证，请使用如下所示的字符串值：

```
{ "license" : "SEE LICENSE IN <filename>" }
```

然后``在包的顶层添加一个名为的文件。

一些旧软件包使用许可证对象或包含许可证对象数组的“ licenses”属性：

```
// Not valid metadata
{ "license" :
  { "type" : "ISC"
  , "url" : "https://opensource.org/licenses/ISC"
  }
}

// Not valid metadata
{ "licenses" :
  [
    { "type": "MIT"
    , "url": "https://www.opensource.org/licenses/mit-license.php"
    }
  , { "type": "Apache-2.0"
    , "url": "https://opensource.org/licenses/apache2.0.php"
    }
  ]
}
```

这些样式现在已弃用。而是使用SPDX表达式，如下所示：

```
{ "license": "ISC" }

{ "license": "(MIT OR Apache-2.0)" }
```

最后，如果您不希望以任何条款授予他人使用私有或未发布软件包的权利：

```
{ "license": "UNLICENSED" }
```

还考虑设置`"private": true`以防止意外发布。

## 8. people fields: author, contributors

“作者”是一个人。“贡献者”是一群人。“人员”是具有“名称”字段以及（可选）“ url”和“ email”的对象，如下所示：

```
{ "name" : "Barney Rubble"
, "email" : "b@rubble.com"
, "url" : "http://barnyrubble.tumblr.com/"
}
```

或者，您可以将所有内容缩短为一个字符串，然后npm会为您解析：

```
"Barney Rubble <b@rubble.com> (http://barnyrubble.tumblr.com/)"
```

电子邮件和url都是可选的。

npm还会为您的npm用户信息设置一个顶级“ maintainers”字段。

## 9. files

可选`files`字段是文件模式数组，描述了将软件包作为依赖项安装时要包括的条目。文件模式遵循与相似的语法`.gitignore`，但是相反：包括文件，目录或全局模式（`*`，`**/*`等）将使它成为打包文件时将其包含在压缩包中。省略该字段将使其默认为`["*"]`，这意味着它将包括所有文件。

某些特殊文件和目录也被包括或排除在外，无论它们是否存在于`files`数组中（请参见下文）。

您还可以`.npmignore`在包的根目录或子目录中提供文件，以防止文件被包含。在程序包的根目录下，它不会覆盖“文件”字段，但在子目录中，它将覆盖。该`.npmignore`文件的工作方式与一样 `.gitignore`。如果有`.gitignore`文件但`.npmignore`缺少文件，`.gitignore`则将使用的内容。

文件包含了“的package.json＃文件”栏中*无法*通过排除`.npmignore`或`.gitignore`。

无论设置如何，总是会包含某些文件：

- `package.json`
- `README`
- `CHANGES`/ `CHANGELOG`/`HISTORY`
- `LICENSE` / `LICENCE`
- `NOTICE`
- “主”字段中的文件

`README`，`CHANGES`，`LICENSE`和`NOTICE`可以有任何情况下和延伸。

相反，某些文件总是被忽略：

- `.git`
- `CVS`
- `.svn`
- `.hg`
- `.lock-wscript`
- `.wafpickle-N`
- `.*.swp`
- `.DS_Store`
- `._*`
- `npm-debug.log`
- `.npmrc`
- `node_modules`
- `config.gypi`
- `*.orig`
- `package-lock.json` （改用收缩包装）

## 10. main

主要字段是模块ID，它是程序的主要入口点。也就是说，如果您的包名为`foo`，并且用户先安装它，然后执行 `require("foo")`，则将返回主模块的导出对象。

这应该是相对于软件包文件夹根目录的模块ID。

对于大多数模块，拥有一个主脚本是最有意义的，而通常没有太多其他东西。

## 11. browser

如果要在客户端使用模块，则应使用浏览器字段而不是主字段。这有助于提示用户它可能依赖于Node.js模块中不可用的原语。（例如`window`）

## 12. bin

许多软件包都具有一个或多个想要安装到PATH中的可执行文件。npm使此操作非常容易（实际上，它使用此功能来安装“ npm”可执行文件。）

要使用此功能，请`bin`在package.json中提供一个字段，该字段是命令名到本地文件名的映射。在安装时，npm会将文件符号链接到 `prefix/bin`以进行全局安装或`./node_modules/.bin/`本地安装。

例如，myapp可能具有以下内容：

```
{ "bin" : { "myapp" : "./cli.js" } }
```

因此，在安装myapp时，它将创建从`cli.js`脚本到 的符号链接`/usr/local/bin/myapp`。

如果您只有一个可执行文件，并且其名称应为程序包的名称，则可以将其作为字符串提供。例如：

```
{ "name": "my-program"
, "version": "1.2.5"
, "bin": "./path/to/program" }
```

将与此相同：

```
{ "name": "my-program"
, "version": "1.2.5"
, "bin" : { "my-program" : "./path/to/program" } }
```

请确保您引用的文件以`bin`开头 `#!/usr/bin/env node`，否则脚本将在没有节点可执行文件的情况下启动！

## 13. man

指定要放置的单个文件或文件名数组，以供 `man`程序查找。

如果仅提供一个文件，则将其安装为来自的结果`man `，而不管其实际文件名如何。例如：

```
{ "name" : "foo"
, "version" : "1.2.3"
, "description" : "A packaged foo fooer for fooing foos"
, "main" : "foo.js"
, "man" : "./man/doc.1"
}
```

将链接`./man/doc.1`文件，使其成为目标`man foo`

如果文件名不是以程序包名称开头的，则使用前缀。所以这：

```
{ "name" : "foo"
, "version" : "1.2.3"
, "description" : "A packaged foo fooer for fooing foos"
, "main" : "foo.js"
, "man" : [ "./man/foo.1", "./man/bar.1" ]
}
```

将创建文件做`man foo`和`man foo-bar`。

手册文件必须以数字结尾，`.gz`如果被压缩，则必须以后缀结尾。该数字指示文件安装在哪个man节中。

```
{ "name" : "foo"
, "version" : "1.2.3"
, "description" : "A packaged foo fooer for fooing foos"
, "main" : "foo.js"
, "man" : [ "./man/foo.1", "./man/foo.2" ]
}
```

将创建条目`man foo`和`man 2 foo`

## 14. directories

CommonJS [Packages](http://wiki.commonjs.org/wiki/Packages/1.0)规范详细介绍了几种使用`directories` 对象指示软件包结构的方法。如果查看[npm的package.json](https://registry.npmjs.org/npm/latest)，您会看到它具有doc，lib和man的目录。

将来，此信息可能会以其他创造性方式使用。

### directorys.lib

告诉人们您的图书馆大部分在哪里。对于lib文件夹，没有任何特别的处理，但这是有用的元信息。

### directorys.bin

如果在中指定`bin`目录`directories.bin`，则将添加该文件夹中的所有文件。

由于该`bin`指令的工作方式，同时指定 `bin`路径和设置`directories.bin`是错误的。如果要指定单个文件，请使用`bin`，对于现有`bin`目录中的所有文件，请使用`directories.bin`。

### directory.man

一个充满手册页的文件夹。糖通过遍历文件夹来生成“ man”数组。

### directory.doc

将markdown文件放在这里。最终，这些可能会在某天很好地显示出来。

### directorys.example

在此处放入示例脚本。有一天，它可能会以某种巧妙的方式暴露出来。

### directorys.test

将您的测试放在这里。它目前尚未公开，但可能会在将来发布。

## 15. repository

指定代码所在的位置。这对想要贡献的人很有帮助。如果git repo在GitHub上，则该`npm docs` 命令将能够找到您。

像这样做：

```
"repository": {
  "type" : "git",
  "url" : "https://github.com/npm/cli.git"
}

"repository": {
  "type" : "svn",
  "url" : "https://v8.googlecode.com/svn/trunk/"
}
```

该URL应该是可公开获得的（也许是只读的）URL，可以直接将其传递给VCS程序，而无需进行任何修改。它不应是您放入浏览器中的html项目页面的url。它用于计算机。

对于GitHub，GitHub gist，Bitbucket或GitLab存储库，您可以使用与以下相同的快捷方式语法`npm install`：

```
"repository": "npm/npm"

"repository": "github:user/repo"

"repository": "gist:11081aaa281"

"repository": "bitbucket:user/repo"

"repository": "gitlab:user/repo"
```

如果`package.json`您的软件包的不在根目录中（例如，如果它是monorepo的一部分），则可以指定其所在的目录：

```
"repository": {
  "type" : "git",
  "url" : "https://github.com/facebook/react.git",
  "directory": "packages/react-dom"
}
```

## 16. scripts

“ scripts”属性是一个字典，其中包含在包的生命周期中的各个时间运行的脚本命令。关键是生命周期事件，而值是此时要运行的命令。

请参阅参考资料，`npm-scripts`以了解有关编写程序包脚本的更多信息。

## 17. config

“ config”对象可用于设置在升级过程中持续存在的程序包脚本中使用的配置参数。例如，如果一个程序包具有以下内容：

```
{ "name" : "foo"
, "config" : { "port" : "8080" } }
```

然后有一个“开始”命令，该命令随后引用了 `npm_package_config_port`环境变量，那么用户可以通过执行覆盖它`npm config set foo:port 8001`。

有关软件包配置的更多信息，请参见`npm-config`和`npm-scripts`。

## 18. dependencies

依赖关系在一个简单的对象中指定，该对象将程序包名称映射到版本范围。版本范围是一个字符串，具有一个或多个以空格分隔的描述符。也可以使用tarball或git URL来识别依赖项。

**请不要在您的`dependencies`物体中放置测试线束或编译器 。** 请参阅`devDependencies`下文。

有关指定版本范围的更多详细信息，请参见[semver](https://www.npmjs.cn/misc/semver)。

- `version`必须`version`完全匹配
- `>version` 必须大于 `version`
- `>=version` 等等
- `
- `<=version`
- `~version`“大约等效于版本”请参见[semver](https://www.npmjs.cn/misc/semver)
- `^version`“与版本兼容”请参见[semver](https://www.npmjs.cn/misc/semver)
- `1.2.x` 1.2.0、1.2.1等，但不是1.3.0
- `http://...` 请参阅下面的“ URL作为依赖项”
- `*` 匹配任何版本
- `""` （只是一个空字符串）与 `*`
- `version1 - version2`与相同`>=version1 <=version2`。
- `range1 || range2` 如果满足range1或range2，则通过。
- `git...` 请参阅下面的“将Git URL作为依赖项”
- `user/repo` 请参阅下面的“ GitHub URL”
- `tag`标记并发布为`tag` See的特定版本`npm-dist-tag`
- `path/path/path`请参阅下面的[本地路径](https://www.npmjs.cn/files/package.json/#local-paths)

例如，这些都是有效的：

```
{ "dependencies" :
  { "foo" : "1.0.0 - 2.9999.9999"
  , "bar" : ">=1.0.2 <2.1.2"
  , "baz" : ">1.0.2 <=2.3.4"
  , "boo" : "2.0.1"
  , "qux" : "<1.0.0 || >=2.3.1 <2.4.5 || >=2.5.2 <3.0.0"
  , "asd" : "http://asdf.com/asdf.tar.gz"
  , "til" : "~1.2"
  , "elf" : "~1.2.3"
  , "two" : "2.x"
  , "thr" : "3.3.x"
  , "lat" : "latest"
  , "dyl" : "file:../dyl"
  }
}
```

### URL作为依赖项

您可以指定tarball URL代替版本范围。

该压缩包将在安装时下载并本地安装到您的软件包中。

### Git URL作为依赖项

Git URL的形式为：

```
<protocol>://[<user>[:<password>]@]<hostname>[:<port>][:][/]<path>[#<commit-ish> | #semver:<semver>]
```

``是以下之一`git`，`git+ssh`，`git+http`，`git+https`，或 `git+file`。

如果`#`提供，它将用于完全克隆该提交。如果commit-ish的格式为`#semver:`，``可以是任何有效的semver范围或确切的版本，并且npm会在远程存储库中查找与该范围匹配的任何标记或引用，就像对注册表依赖项一样。如果未指定`#`或`#semver:`，则`master`使用。

例子：

```
git+ssh://git@github.com:npm/cli.git#v1.0.27
git+ssh://git@github.com:npm/cli#semver:^5.0
git+https://isaacs@github.com/npm/cli.git
git://github.com/npm/cli.git#v1.0.27
```

### GitHub网址

从1.1.65版本开始，您可以将GitHub URL简称为“ foo”：“ user / foo-project”。与git URL一样，`commit-ish`可以包含后缀。例如：

```
{
  "name": "foo",
  "version": "0.0.0",
  "dependencies": {
    "express": "expressjs/express",
    "mocha": "mochajs/mocha#4727d357ea",
    "module": "user/repo#feature\/branch"
  }
}
```

### 本地路径

从2.0.0版开始，您可以提供包含软件包的本地目录的路径。可以使用`npm install -S`或 `npm install --save`使用以下任意形式保存本地路径：

```
../foo/bar
~/foo/bar
./foo/bar
/foo/bar
```

在这种情况下，它们将被标准化为相对路径并添加到您的中 `package.json`。例如：

```
{
  "name": "baz",
  "dependencies": {
    "bar": "file:../foo/bar"
  }
}
```

此功能对于本地脱机开发和创建需要npm安装的测试很有用，您不想在不希望访问外部服务器的地方安装npm，但是在将程序包发布到公共注册表时不应使用。

## 19. devDependencies

如果有人计划在其程序中下载和使用您的模块，那么他们可能不希望或不需要下载并构建您使用的外部测试或文档框架。

在这种情况下，最好将这些其他项目映射到一个`devDependencies` 对象中。

这些东西将在安装时`npm link`或`npm install` 从软件包的根目录安装，并且可以像其他任何npm配置参数一样进行管理。有关`npm-config`更多信息，请参见。

对于不是特定于平台的构建步骤（例如，将CoffeeScript或其他语言编译为JavaScript），请使用`prepare` 脚本来执行此操作，并使所需的软件包成为devDependency。

例如：

```
{ "name": "ethopia-waza",
  "description": "a delightfully fruity coffee varietal",
  "version": "1.2.3",
  "devDependencies": {
    "coffee-script": "~1.6.3"
  },
  "scripts": {
    "prepare": "coffee -o lib/ -c src/waza.coffee"
  },
  "main": "lib/waza.js"
}
```

该`prepare`脚本将在发布之前运行，因此用户可以使用该功能，而无需他们自己对其进行编译。在开发人员模式下（即本地运行`npm install`），它也会运行此脚本，以便您可以轻松对其进行测试。

## 20.  peerDependencies

在某些情况下，您想表达软件包与主机工具或库的兼容性，而不必一定要`require`对此主机进行操作。通常将其称为*插件*。值得注意的是，您的模块可能正在公开主机文档预期和指定的特定接口。

例如：

```
{
  "name": "tea-latte",
  "version": "1.3.5",
  "peerDependencies": {
    "tea": "2.x"
  }
}
```

这样可以确保您的软件包`tea-latte`只能与主机软件包的第二个主要版本*一起*安装`tea`。`npm install tea-latte`可能会产生以下依赖关系图：

```
├── tea-latte@1.3.5
└── tea@2.2.0
```

**注意：`peerDependencies`如果在依赖关系树中未显式依赖更高版本的npm版本1和2 ，它们将自动安装。在npm的下一个主要版本（npm @ 3）中，情况将不再如此。您将收到一条警告，提示您未安装peerDependency。**npms 1和2中的行为经常令人困惑，很容易使您陷入依赖地狱，npm旨在避免这种情况。

尝试安装其他要求冲突的插件会导致错误。因此，请确保您的插件要求尽可能广泛，并且不要将其锁定为特定的补丁程序版本。

假设主机符合[semver](https://semver.org/)，则只有主机包主版本中的更改会破坏您的插件。因此，如果您已经使用了主机软件包的每个1.x版本，请使用`"^1.0"`或`"1.x"`表示这一点。如果您依赖1.5.2中引入的功能，请使用`">= 1.5.2 < 2"`。

## 21. bundledDependencies

这定义了一组软件包名称，这些名称将在发布软件包时捆绑在一起。

如果您需要在本地保留npm软件包或通过单个文件下载获得它们，则可以通过在`bundledDependencies` 数组中指定软件包名称并执行来将软件包捆绑在tarball文件中`npm pack`。

例如：

如果我们这样定义package.json：

```
{
  "name": "awesome-web-framework",
  "version": "1.0.0",
  "bundledDependencies": [
    "renderized", "super-streams"
  ]
}
```

我们可以`awesome-web-framework-1.0.0.tgz`通过运行获取文件`npm pack`。此文件包含的依赖关系`renderized`，并`super-streams`可以通过执行安装在一个新的项目`npm install awesome-web-framework-1.0.0.tgz`。

如果这是拼写的`"bundleDependencies"`，那么也很荣幸。

## 22. optionalDependencies

如果可以使用依赖项，但是如果找不到或无法安装npm，则希望npm继续，则可以将其放入`optionalDependencies` 对象中。这是程序包名称到版本或url的映射，就像 `dependencies`对象一样。区别在于构建失败不会导致安装失败。

处理依赖关系的缺乏仍然是程序的责任。例如，如下所示：

```
try {
  var foo = require('foo')
  var fooVersion = require('foo/package.json').version
} catch (er) {
  foo = null
}
if ( notGoodFooVersion(fooVersion) ) {
  foo = null
}

// .. then later in your program ..

if (foo) {
  foo.doFooThings()
}
```

中的条目`optionalDependencies`将覆盖中的同名条目 `dependencies`，因此通常最好只放在一个位置。

## 23. engines

您可以指定代码可以运行的node版本：

```
{ "engines" : { "node" : ">=0.10.3 <0.12" } }
```

并且，与依赖项一样，如果您不指定版本（或者如果您指定“ *”作为版本），那么任何版本的节点都可以。

如果指定“引擎”字段，则npm将要求“节点”在该列表中的某个位置。如果省略了“ engines”，那么npm只会假设它在节点上有效。

您也可以使用“引擎”字段来指定哪些版本的npm能够正确安装程序。例如：

```
{ "engines" : { "npm" : "~1.0.20" } }
```

除非用户设置了`engine-strict`config标志，否则此字段仅供参考，仅在将软件包作为依赖项安装时才产生警告。

## 24. engineStrict

**此功能已在npm 3.0.0中删除**

在npm 3.0.0之前，此功能用于将此软件包视为用户已设置`engine-strict`。不再使用。

## 25. os

您可以指定模块将在哪些操作系统上运行：

```
"os" : [ "darwin", "linux" ]
```

您也可以将操作系统列入黑名单而不是白名单，只需在黑名单上添加“！”即可：

```
"os" : [ "!win32" ]
```

主机操作系统由 `process.platform`

尽管没有充分的理由这样做，但允许将其列入黑名单和白名单。

## 26. cpu

如果您的代码仅在某些cpu体系结构上运行，则可以指定哪些体系结构。

```
"cpu" : [ "x64", "ia32" ]
```

像该`os`选项一样，您也可以将体系结构列入黑名单：

```
"cpu" : [ "!arm", "!mips" ]
```

主机架构由 `process.arch`

## 27. preferredGlobal

 **DEPRECATED** 

该选项用于触发npm警告，但不再发出警告。纯粹出于提供信息的目的。现在建议您尽可能将任何二进制文件安装为本地devDependencies。

## private

如果`"private": true`在package.json中设置，则npm将拒绝发布它。

这是防止意外发布私有存储库的方法。如果要确保仅将给定的程序包发布到特定的注册表（例如，内部注册表），请使用`publishConfig`下面描述的 字典`registry`在发布时覆盖config参数。

## 28. publishConfig

这是将在发布时使用的一组配置值。如果要设置标签，注册表或访问权限，这特别方便，这样可以确保给定的程序包不标记为“最新”，发布到全局公共注册表中，或者默认情况下作用域模块是私有的。

可以覆盖任何配置值，但是对于发布而言，仅“ tag”，“ registry”和“ access”可能重要。

请参阅`npm-config`以查看可以覆盖的配置选项列表。

## 默认值

npm会根据软件包内容填充一些默认值。

- `"scripts": {"start": "node server.js"}`

  如果在包根目录中有一个文件`server.js`，则npm将默认`start`命令为`node server.js`。

- `"scripts":{"install": "node-gyp rebuild"}`

  如果包根目录中有一个文件`binding.gyp`，并且您尚未定义`install`或`preinstall`脚本，则npm将默认`install`使用node-gyp编译该命令。

- `"contributors": [...]`

  如果您的包根目录中有一个文件`AUTHORS`，npm会将每行视为一种 name email url 格式，其中email和url是可选的。以a `#`或空白开头的行将被忽略。