# 时记 - 随时随地，记录工时
Keep track of your working hours.

## v1.0.0 版本简介
支持 Android 8.0 及以上，记录保存在手机本地，不需要账户或联网。

* **安装包**：ShijiOffline-debug.apk
* **完整源码**：ShijiOffline-source.zip



**代码采用 Java＋安卓原生界面＋SQLite。** 主要文件如下：

| 文件                  | 作用                  |
| ------------------- | ------------------- |
| `MainActivity.java` | 打卡按钮、底部切换、日历、记录编辑界面 |
| `WorkDb.java`       | 本地数据库，保存和增删改打卡记录    |
| `TimeMath.java`     | 工时计算、跨日拆分、每周统计      |
| `HolidayRules.java` | 判断节假日是否自动加 8 小时     |
| `holidays-2026.csv` | 内置节假日数据，可通过本地文件更新   |

计时逻辑采用**保存时间戳，而不是让后台一直运行计时器**，所以锁屏、退出 App、重启手机之后，记录仍然存在。

跨午夜的工时会自动分摊到两天。


## 自行构建 APK

**按以下步骤操作即可：**

1. 安装 [Android Studio](https://developer.android.com/studio)，解压源码包(.zip后缀文件)，选择 **Open** 打开 `ShijiOffline` 根目录。

2. 在 **SDK Manager** 安装 Android SDK Platform 35 和 Build-Tools 35.0.0。

3. 将 **Gradle JDK 设置为 17**。项目已配置 AGP 8.9.2、Gradle 8.11.1，与上述环境匹配。

4. 等待项目同步完成，在 Android Studio 的 Terminal 中执行：

   Windows：

   ```powershell
   .\gradlew.bat :app:assembleDebug
   ```

   macOS / Linux：

   ```bash
   chmod +x gradlew
   ./gradlew :app:assembleDebug
   ```

5. 构建成功后，安装包位于：

   ```text
   app/build/outputs/apk/debug/app-debug.apk
   ```
   
   `assembleDebug` 生成的 APK 已带测试签名，可以复制到手机安装。

**首次构建时电脑通常需要联网下载工具，但安装后的 App 完全离线使用。** 作者提供的 APK 使用 SDK 直接打包生成，对应脚本也包含在源码中。

## 年份导入

该 App 要求 CSV 三列，第一行固定为：

```csv
date,type,name
```

例如，`holidays-2026.csv` 的内容可以这样写：

```csv
date,type,name
2026-01-01,LEGAL,元旦
2026-01-02,OFF,元旦调休
2026-01-04,WORK,元旦补班
2026-02-16,LEGAL,除夕
2026-02-17,LEGAL,春节
2026-02-18,LEGAL,春节
2026-02-19,LEGAL,春节
```

三列的含义如下：

| 列名     | 含义   | 填写方式                    |
| ------ | ---- | ----------------------- |
| `date` | 日期   | `年-月-日`，例如 `2026-01-01` |
| `type` | 日期类型 | `LEGAL`、`OFF` 或 `WORK`  |
| `name` | 显示名称 | 例如“元旦”“春节补班”            |

类型决定是否自动补工时：

| 类型      | 含义     | 自动补时规则          |
| ------- | ------ | --------------- |
| `LEGAL` | 法定节日本日 | 落在周一至周五时，加 8 小时 |
| `OFF`   | 调休、补休  | 不自动加时           |
| `WORK`  | 补班     | 不自动加时，按实际打卡计算   |

**注意：春节连休的全部日期，不能全部填成 `LEGAL`。** 法定节日与调休形成的假期要分开。

制作时注意：

* 使用**英文逗号**，类型填写大写英文。
* 日期补齐两位，例如 `2026-01-01`，同一天不能重复。
* 名称不要包含英文逗号。
* 保存为 **UTF-8 编码的 `.csv` 文件**。用 Excel 时可选择“CSV UTF-8（逗号分隔）”。

**上面只是格式示例，不是完整年度数据。当前 App 导入时会替换文件涉及年份的节假日表，因此实际导入应使用完整年度 CSV。** 源码包的 `app/src/main/assets/holidays-2026.csv` 中已有完整的 2026 年配置。


## 补充说明
当前内置 **2026 年节假日**，其他年份可导入本地 CSV。

完整操作、代码讲解和正式签名步骤都在源码包的 `README.md` 中。

正式长期使用前建议生成自己的签名版本；卸载或清除应用数据会删除本地工时。
