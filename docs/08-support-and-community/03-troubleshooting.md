---
sidebar_label: 🔧 Troubleshooting
---

# 🔧 Troubleshooting

Having trouble using Serverpod? Here are common issues and solutions to help you get back on track quickly.

## ⚡ Common Problems


### Flutter app fails to connect to the server
- **Check:** Is your server running on `http://localhost:8080/`?
- **Fix:** If running on a physical device, replace `localhost` with your machine’s IP address.
- **Tip:** Use `flutter run --dart-define=SERVER_URL=http://<your-ip>:8080`.

### Dart `FormatException` when calling endpoints
- **Cause:** Input validation failed (e.g., out-of-bounds index).
- **Fix:** Make sure you send valid parameters to endpoints.

### Docker DB not reachable
- **Check:** Is Docker running?
- **Fix:** Try restarting Docker or running `docker compose up -d` again.
- **Note:** Wait a few seconds after DB starts before running migrations.

## 🔧 Debugging Tips

- Use `print()` or logging in both Flutter and Serverpod to trace logic.
- Add `--apply-migrations` to your server run command during dev.
- Use Serverpod Insights to track performance and errors.

## 💬 Still stuck?

- Ask in our [GitHub Discussions](https://github.com/serverpod/serverpod/discussions).
- Reach out in the [Discord Community](https://discord.gg/N5zAX3zTnT).
- File a [bug report](https://github.com/serverpod/serverpod/issues) if you think something is broken.

Happy debugging! 🚀
