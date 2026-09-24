# LetianpaiService

Privileged Android app that is the command bus for the other system apps. It does not move the robot, open a serial port, or call the cloud. It only forwards commands to whoever registered a callback.

## Package

- `applicationId` `com.renhejia.robot.letianpaiservice`
- `android:sharedUserId="android.uid.system"`
- Service filter: `android.intent.action.LETIANPAI`
- `onBind` returns `ILetianpaiService.Stub`

The AIDL types come from the `GeeUIComponets` submodule (`CommChannel`). `git submodule update` is required. `settings.gradle` looks for `GeeUIComponents/...` (different spelling from the submodule path `GeeUIComponets`).

## What happens on a command

A client binds the service and calls `setCommand`, `setMcuCommand`, `setLongConnectCommand`, `setAudioEffect`, `setExpression`, `setAppCmd`, `setTTS`, `setSpeechCmd`, `setSensorResponse`, `setMiCmd`, `setIdentifyCmd`, or `setBleCmd`.

The service posts a `CmdInfo` onto that channel's `Handler`, then walks a `RemoteCallbackList` and calls `onCommandReceived` (or the matching typed callback). If `robotStatus` is `ROBOT_OTA_STATUS` (`1`), `setCommand` does nothing so an OTA update is not interrupted.

`setRobotStatus` stores an int and notifies `onRobotStatusChanged`.

## Comment glossary

| Where | Chinese | English |
|---|---|---|
| `LetianpaiService.java` | 分发命令回调 | Dispatch the command to callbacks |
| `LetianpaiService.java` | 异常 | Exception (used in several `Log.e` strings) |

## Build

`:app` compileSdk 32, minSdk 21. Signing uses `keystore/letianpai.jks`, which is not in the tree. Do not commit keystore passwords.
