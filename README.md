# Command Line Tools for HarmonyOS 

> The Command Line Tools for HarmonyOS for GitHub Actions

see in [releases](/releases)

Use example:

```yml
name: CI

on:
  push:
    branches:
      - master
  pull_request:

jobs:
  build:
    runs-on: macos-latest

    steps:
      - uses: actions/checkout@v2

      - uses: actions/setup-java@v4
        with:
          distribution: 'zulu' # See 'Supported distributions' for available options
          java-version: '17'

      - name: Set up Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '16'

      - name: Get Release Assets
        id: get_assets
        run: |
          # 获取 release 的信息
          RELEASE_JSON=$(curl -s -H "Accept: application/vnd.github.v3+json" https://api.github.com/repos/harmonyos-dev/hos-sdk/releases/latest)
          
          # 从 release 的信息中提取需要的 asset URL
          ASSET_URL=$(echo "$RELEASE_JSON" | jq -r '.assets[] | select(.name | test("commandline-tools-mac-2.0.0.2.zip")) | .browser_download_url')
          
          # 输出 asset URL
          echo "Asset URL: $ASSET_URL"
          
          # 下载 release asset
          curl -LJO "$ASSET_URL"

      - name: Unzip Release Assets
        run: |
          unzip commandline-tools-mac-2.0.0.2.zip

      - name: Install HarmonyOS SDK
        run: |
          # chmod +x commandline
          # for handle Failed to request url https://devecostudio-dre.op.hicloud.com/sdkmanager/v5/hos/getSdkList
          #[                    ]0%  
          export JAVA_TOOL_OPTIONS="$JAVA_TOOL_OPTIONS -Duser.country=CN"
          
          chmod +x command-line-tools/sdkmanager/bin/sdkmgr
          # 安装 HarmonyOS SDK
          command-line-tools/sdkmanager/bin/sdkmgr install toolchains:9 OpenHarmony/toolchains:9 --accept-license
          # get current dir and export to HOS_SDK_HOME

      - name: Install dependencies
        run: npm ci

      - name: Build
        run: |
          # for ERROR: Unable to find 'hwsdk.dir' in 'local.properties' or 'HOS_SDK_HOME' in the system environment path.
          export HOS_SDK_HOME=$(pwd)/hwsdk
          ./node_modules/.bin/hvigor --mode module -p product=default assembleHap --no-daemon --accept-license

```

