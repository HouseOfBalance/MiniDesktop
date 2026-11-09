# Mini PC Desktop Environment (Android / Kotlin / Compose)

Biến điện thoại thành môi trường Desktop: Top Dock + Start Menu kiểu Windows 11, cửa sổ Freeform,
hình nền video/GIF, Control Center, thông báo, File Explorer, Task Manager.

## Chạy thử
1. Mở thư mục này bằng **Android Studio Koala+** (JDK 17), đợi Gradle sync.
2. Cài & chạy Shizuku (hoặc máy đã root) trên thiết bị Android 8.0+.
3. Run app → làm theo Onboarding 4 bước (Shizuku/Root → Đọc thông báo → Truy cập tệp → Home mặc định) → **Khởi động Mini PC**.

## Cứu hộ nếu giao diện lỗi
- Chạm **5 lần** vào góc trên-trái trong 3 giây, hoặc giữ **Vol+ & Vol-** 3 giây → reset DPI, thoát Kiosk, về Launcher gốc.
- Hoặc dùng adb: `adb shell wm density reset`.

## Ánh xạ 10 giai đoạn → code
| GĐ | Nội dung | File chính |
|---|---|---|
| 1 | Gradle, package, Room, DataStore | `app/build.gradle.kts`, `data/db/Database.kt`, `data/preferences/SettingsStore.kt` |
| 2 | Shizuku/Root, cờ Freeform, DPI | `core/shizuku/*` |
| 3 | Kiosk, Home, Back, bàn phím/chuột | `core/kiosk/Kiosk.kt`, `ui/MainActivity.kt`, `ui/common/Common.kt` (secondaryClick) |
| 4 | Wallpaper Engine | `data/repository/WallpaperRepository.kt`, `ui/desktop/WallpaperLayer.kt` |
| 5 | Lưới shortcut, kéo thả, SpiralSearch | `core/window/WindowMath.kt`, `domain/usecase/Algorithms.kt`, `ui/desktop/DesktopGrid.kt` |
| 6 | Top Dock, Start Menu, Fuzzy | `ui/topdock`, `ui/startmenu`, `data/repository/AppRepository.kt` |
| 7 | System Tray, Control Center, thông báo | `ui/systemtray`, `data/repository/NotificationRepository.kt` |
| 8 | Freeform launcher, snap, Task Manager | `domain/usecase/WindowUseCases.kt`, `ui/desktop/WindowHelperOverlay.kt`, `ui/taskmanager` |
| 9 | File Explorer dạng cửa sổ | `ui/fileexplorer` |
| 10 | Panic, crash handler, Onboarding | `core/utils/FailSafe.kt`, `ui/onboarding`, `ui/DesktopScreen.kt` |

## Lưu ý kỹ thuật
- `am start` **không có** tuỳ chọn `--bounds`; app mở bằng `--windowingMode 5` rồi `am task resize <taskId> l t r b`.
- Shizuku `newProcess` là private → gọi qua reflection (`ShellExecutor`).
- Screen pinning (`kioskLocked`) mặc định **tắt** vì chế độ pin chặn mở app khác (làm hỏng Freeform).
- Danh sách cửa sổ đọc từ `dumpsys activity activities` – định dạng khác nhau giữa các ROM, parse theo kiểu best-effort.
- "Thu nhỏ" = Show Desktop (đưa Desktop lên trên), vì Android không có lệnh minimize từng task qua shell.
