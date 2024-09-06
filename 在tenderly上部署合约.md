# `在tenderly上部署合约`

## 参考资料

<https://github.com/Tenderly/tenderly-cli>

[https://learnblockchain.cn/article](https://learnblockchain.cn/article/5774)**[/](https://learnblockchain.cn/article/5774)**[5774](https://learnblockchain.cn/article/5774)

## 配置Hardhat

### 创建目录

```shell
mkdir FxMockPrice
cd FxMockPrice
```

### 初始化Node项目

    npm init

### 安装运行hardhat

    npm install --save-dev hardhat

在安装**Hardhat**的目录下运行：

    npx hardhat (deprecated)
    npx hardhat init

使用键盘选择"创建一个新的hardhat.config.js（`Create a JavaScript project`）" ，然后回车。

这个 JavaScript Hardhat 工程会默认下载 `hardhat-toolbox 插件`及一些常规设置：

创建好的Hardhat工程包含文件有：

*   `contracts`：智能合约目录
*   `scripts` ：部署脚本文件
*   `test`：智能合约测试用例文件夹。
*   `hardhat.config.js`：配置文件，配置hardhat连接的网络及编译选项。

## Tenderly CLI Installation

### macOS

You can install the Tenderly CLI via the [Homebrew package manager](https://brew.sh/):

```bash
brew tap tenderly/tenderly
brew install tenderly
```

Or if you prefer you can also install by using cURL and running our installation script:

```bash
curl https://raw.githubusercontent.com/Tenderly/tenderly-cli/master/scripts/install-macos.sh | sh
```

### Linux

You can install the Tenderly CLI by using cURL and running our installation script.

With `root` privileges user:

```bash
curl https://raw.githubusercontent.com/Tenderly/tenderly-cli/master/scripts/install-linux.sh | sh
```

Or with `sudo` user:

```shell
curl https://raw.githubusercontent.com/Tenderly/tenderly-cli/master/scripts/install-linux.sh | sudo sh
```

### Windows

Go to the [release page](https://github.com/Tenderly/tenderly-cli/releases), download the latest version and put it
somewhere in your `$PATH`.

### Updating

You can check the current version of the CLI by running:

```shell
tenderly version
```

To upgrade it via Homebrew:

```shell
brew upgrade tenderly
```

### tenderly login

The `login` command is used to authenticate the Tenderly CLI with your [Tenderly Dashboard](https://dashboard.tenderly.co/).

    tenderly login

### initialize tenderly project

To install hardhat-tenderly run.

&#x9;`npm install --save-dev @tenderly/hardhat-tenderly`

Add the following statement to your `hardhat.config.js`:

&#x9;`require("@tenderly/hardhat-tenderly");`

Or, if you are using typescript:

```typescript
import "@tenderly/hardhat-tenderly"
```

Notes: Run: `npx hardhat node --network hardhat` to start a local node

## deploy contract to tenderly

### 创建部署脚本

Then you need to call it from your scripts (using ethers to deploy a contract deploy.js):

```javascript
async function main() {
    const [deployer] = await ethers.getSigners();
    console.log(deployer);
    console.log("Deploying contracts with the account:", deployer.address);

        // 获取合约工厂并部署
        const Contract = await ethers.getContractFactory("FxMockPrice");
        const contract = await Contract.deploy();
        console.log(contract);
        console.log("Contract deployed to address:", contract.address);
        console.log(contract.address)

        // 在 Tenderly 上推送合约
        await tenderly.persistArtifacts({
          name: "FxMockPrice",
          address: contract.address,
        });

        console.log("Contract pushed to Tenderly.");

    }

    main()
    .then(() => process.exit(0))
    .catch(error => {
    console.error(error);
    process.exit(1);
    });

```

Run: `npx hardhat compile` to compile contracts

### 配置hardhat.config.js

    accounts: 个人地址私钥
    module.exports = {
      solidity: "0.8.24",
      networks: {
        tenderly: {
          // url: "https://rpc.tenderly.co/testnet/ed1c21fe-56c0-4d81-ad20-6ae724a6493d",
          // url: "https://virtual.mainnet.rpc.tenderly.co/e49bbe50-22c7-4181-bd6b-57601e071766",
          url: "https://virtual.mainnet.rpc.tenderly.co/2e32c9e6-495c-4ee8-9584-34d08f75fcff",
          accounts: ["0xe4e23e4e2fa412814e646b2e08f5497c3500451e58e7dc3f58edbbf1e89536d1"],
        },
        sepolia: {
          url: "https://sepolia.infura.io/v3/84c7181458904c209d1445ba1b0a5511",
          accounts: ["0xe4e23e4e2fa412814e646b2e08f5497c3500451e58e7dc3f58edbbf1e89536d1"]
        },
        // 其他网络配置
      },
      tenderly: {
        project: "fxv3",
        username: "Gordon3",
      }
    };

Run: `npx hardhat run scripts/deploy.js --network tenderly`



## 遇到的问题

### &#x20;问题1 Cannot find module '@nomicfoundation/hardhat-chai-matchers'

&#x9;npx hardhat compile An unexpected error occurred: Error: Cannot find module '@nomicfoundation/hardhat-chai-matchers'

以下是解决这个问题的一些步骤：

#### 1. 安装缺少的依赖

首先，你可以尝试手动安装缺失的模块：

```
bashCopy codenpm install --save-dev @nomicfoundation/hardhat-chai-matchers

```

#### 2. 更新或重新安装 `hardhat` 及相关依赖

如果手动安装未解决问题，可以尝试更新或重新安装 `hardhat` 及相关依赖：

1.  删除 `node_modules` 和 `package-lock.json`：

    ```
    bashCopy coderm -rf node_modules package-lock.json

    ```
2.  再次安装所有依赖：

    ```
    bashCopy codenpm install

    ```
3.  重新安装 `hardhat`：

    ```
    bashCopy codenpm install --save-dev hardhat

    ```
4.  再次尝试编译：

    ```
    bashCopy codenpx hardhat compile

    ```

#### 3. 检查 `hardhat.config.js` 或 `hardhat.config.ts`

&#x9;有时错误可能是由于 `hardhat` 配置文件中导入的模块不存在，特别是如果你的项目引用了 `@nomicfoundation/hardhat-chai-matchers` 而未安装它。检查 `hardhat.config.js` 或 `hardhat.config.ts`，确保其中没有未安装的插件。

例如，确认配置文件中是否包含类似于以下内容：

```
javascriptCopy coderequire("@nomicfoundation/hardhat-chai-matchers");

```

如果不需要 `chai matchers`，你可以暂时注释掉该行，并再次尝试运行 `npx hardhat compile`。

#### 4. 确保 Node.js 版本兼容

某些版本的 `hardhat` 可能需要特定版本的 Node.js。可以通过以下命令检查你的 Node.js 版本：

```
bashCopy codenode -v

```

确保你的 Node.js 版本是 12.x、14.x 或者 16.x，推荐使用 LTS 版本。若版本不兼容，考虑升级或降级 Node.js。

#### 5. 使用 `yarn` 替代 `npm`

有时使用 `yarn` 进行依赖管理可以避免 `npm` 带来的问题。你可以尝试用 `yarn` 进行依赖安装：

1.  首先安装 `yarn`：

    ```
    bashCopy codenpm install -g yarn

    ```
2.  使用 `yarn` 安装依赖：

    ```
    bashCopy codeyarn install

    ```
3.  然后运行编译：

    ```
    bashCopy codenpx hardhat compile

    ```

#### 6. 检查 Hardhat 和插件版本的兼容性

如果你的项目使用了多个 Hardhat 插件，确保这些插件与 `hardhat` 版本兼容。有时不同版本之间可能会出现不兼容的情况。

#### 总结

通过手动安装缺失的模块，重新安装依赖或检查配置文件，大多数情况下都可以解决这个问题。如果问题仍然存在，可以检查 `hardhat` 和插件的版本兼容性，并确保你的 Node.js 版本与之匹配。

### 问题2 安装hardhat 报错 ERR! code ENOENT

`ERR! code ENOENT` 错误通常表示在安装 `hardhat` 时，某个文件或目录找不到。要解决这个问题，可以采取以下几种常见步骤：

#### 1. 确保你已经初始化 npm 项目

在项目的根目录下运行以下命令来初始化 `npm` 项目：

```
bashCopy codenpm init -y

```

这会生成一个 `package.json` 文件，确保项目有正确的依赖管理环境。

#### 2. 清理 npm 缓存

有时，npm 缓存中的问题可能导致此类错误。可以通过清理 npm 缓存来解决：

```
bashCopy codenpm cache clean --force

```

然后再次尝试安装 `hardhat`：

```
bashCopy codenpm install --save-dev hardhat

```

#### 3. 检查文件路径和权限

*   确保你在正确的目录中运行命令。错误的路径可能导致 `ENOENT` 错误。
*   确保你有足够的权限运行命令。尝试使用管理员权限（Windows 上使用 `CMD` 或 `Powershell`，macOS/Linux 上使用 `sudo`）：

    ```
    bashCopy codesudo npm install --save-dev hardhat

    ```

#### 4. 检查 `node_modules` 和 `package-lock.json`

如果你之前安装过依赖，可能是某些文件损坏。尝试删除 `node_modules` 目录和 `package-lock.json` 文件，然后重新安装所有依赖：

```
bashCopy coderm -rf node_modules package-lock.json
npm install

```

再尝试安装 `hardhat`：

```
bashCopy codenpm install --save-dev hardhat

```

#### 5. 重新安装 Node.js 和 npm

有时，Node.js 或 npm 安装出现问题也会导致 `ENOENT` 错误。可以尝试重新安装最新版本的 Node.js 和 npm。

1.  卸载旧版本的 Node.js 和 npm。
2.  从 [Node.js 官方网站](https://nodejs.org/) 下载并安装最新版本。

#### 6. 使用 Yarn 作为替代

如果问题依然存在，你可以尝试使用 `yarn` 来替代 `npm`，因为 `yarn` 也可以管理依赖并可能避免某些 `npm` 的问题：

```
bashCopy codenpm install -g yarn
yarn add --dev hardhat

```

#### 7. 查看 npm 日志

如果问题依然无法解决，可以查看详细的 npm 日志文件（通常位于 `npm-debug.log`），分析具体的错误信息，并根据提示进行修复。

