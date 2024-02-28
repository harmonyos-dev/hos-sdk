# OpenHarmony SDK

> The OpenHarmony SDK for GitHub Actions

see in [releases](/releases)

Use example:

```yml

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

