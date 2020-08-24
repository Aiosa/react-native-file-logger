# React-native-file-logger

_A simple file-logger for React Native with configurable rolling policy, based on CocoaLumberjack on iOS and Logback on Android._

## Features
* **💆‍♂️ Easy to setup**: Just call `FileLogger.configure()` and you're done. All your existing `console.log/debug/...` calls are automatically logged into a file
* **🌀 File rolling**: Support for time and size-based file rolling. Max number of files and size-limit can be configured. File rolling can also be disabled.
* **📬 Email support**: Logging into a file is useless if users cannot send logs back to developers. With react-native-file-logger, file logs can be sent by email without having to rely on another library
* **🛠 TypeScript support**: Being written entirely in TypeScript, react-native-file-logger has always up-to-date typings

## How it works
React-native-file-logger uses the [undocumented](https://github.com/facebook/react-native/blob/3c9e5f1470c91ff8a161d8e248cf0a73318b1f40/Libraries/polyfills/console.js#L433) `global.__inspectorLog` from React-native. It allows to intercept any calls to `console` and to retrieve the already-formatted log string. React-native-file-logger uses file-loggers from [CocoaLumberjack](https://github.com/CocoaLumberjack/CocoaLumberjack) on iOS and [Logback Android](https://github.com/tony19/logback-android) on Android to append logs into files with an optional rolling policy.

## Installation

```sh
npm i react-native-file-logger
npx pod-install
```

## Getting started

```
import { FileLogger } from "react-native-file-logger";

FileLogger.configure();
```

This is all you need to add file-logging to your app. `FileLogger.configure()` also takes options to customize the rolling policy, log directory path or log-level.

## API

#### FileLogger.configure(options?): Promise

Initialize the file-logger with the specified options. As soon as the returned promise is resolved, all `console` calls are appended to a log file. To ensure that no logs are missing, it is good practice to `await` this call at the launch of your app.

| Option | Description | Default |
| --- | --- | --- |
| `logLevel` | Minimum log-level for file output (it won't affect console output) | LogLevel.Debug |
| `captureConsole` | If `true`, all `console` calls are automatically captured and written to a log file. It can be changed by calling the `enableConsoleCapture()` and `disableConsoleCapture()` methods  | `true` |
| `dailyRolling` | If `true`, a new log file is created every day | `true` |
| `maximumFileSize` | A new log file is created when current log file exceeds the given size in bytes. `0` to disable | `1024 * 1024` (1MB) |
| `maximumNumberOfFiles` | Maximum number of log files to keep. When a new log file is created, if the total number of files exceeds this limit, the oldest file is deleted. `0` to disable | `5` |
| `logsDirectory` | Absolute path of directory where log files are stored. If not defined, log files are stored in the cache directory of the app | `undefined` |

#### FileLogger.sendLogFilesByEmail(options?): Promise

Send all log files by email. On iOS, it uses `MFMailComposeViewController` to ensure that the user won't leave the app when sending log files.

| Option | Description |
| --- | --- |
| `to` | Email address of the recipient |
| `subject` | Email subject |
| `body` | Plain text body message of the email |

#### FileLogger.enableConsoleCapture()

Enable automatically writing logs from `console` calls to the current log file. It is already enabled by `FileLogger.configure()` by default.

#### FileLogger.disableConsoleCapture()

After calling this method, `console` calls will no longer be appended to the current log file.

#### FileLogger.setLogLevel(logLevel)

Change the minimum log-level for file-output. The initial log-level can be passed as an option to `FileLogger.configure()`.

#### FileLogger.getLogLevel(): LogLevel

Return the current log-level.

#### FileLogger.getLogFilePaths(): Promise<string[]>

Returns a promise with the absolute paths to the log files.

#### FileLogger.deleteLogFiles(): Promise

Remove all log files. Next `console` calls will be appended to a new empty log file.

## Direct access API

If you don't want to use `console` calls for file-logging, you can directly use the following methods to directly write to the log file. It is encouraged to wrapped these calls around your own logger API.

### FileLogger.debug(str)

Shortcut for `FileLogger.write(LogLevel.Debug, str)`.

### FileLogger.info(str)

Shortcut for `FileLogger.write(LogLevel.Info, str)`.

### FileLogger.warn(str)

Shortcut for `FileLogger.write(LogLevel.Warning, str)`.

### FileLogger.error(str)

Shortcut for `FileLogger.write(LogLevel.Error, str)`.

### FileLogger.write(level, str)

Append the given string to the log file with the specified log level. The string will be formatted with the current time and log-level.

### FileLogger.write(level, str)

Append the given string to the log file with the specified log level. The string will be formatted with the current time and log-level.

### FileLogger.writeRaw(level, str)

Append the given string to the log file with the specified log level. The string will be written unformatted.
