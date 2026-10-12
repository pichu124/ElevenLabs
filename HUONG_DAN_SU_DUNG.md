# Hướng dẫn sử dụng VoiceStudio (tiếng Việt)

Repo này là bản sao của [debpalash/VoiceStudio](https://github.com/debpalash/VoiceStudio)
(giữ nguyên toàn bộ lịch sử commit). Tài liệu gốc bằng tiếng Anh nằm ở
[`README.md`](README.md) và thư mục [`docs/`](docs/). File này tóm tắt bằng tiếng Việt
những gì cần biết để cài và dùng app.

---

## 1. VoiceStudio là gì?

Một ứng dụng desktop mã nguồn mở, chạy **trên máy của bạn** (không cần gửi dữ liệu lên
cloud), làm được những việc tương tự ElevenLabs:

| Tính năng | Mô tả ngắn |
|---|---|
| **Voice cloning** | Nhân bản giọng nói từ một đoạn ghi âm mẫu, rồi đọc văn bản bất kỳ bằng giọng đó |
| **Voice design** | Tạo giọng mới chỉ bằng mô tả (nam/nữ, tuổi, cao độ, giọng vùng miền…) — không cần file mẫu |
| **Dub** (lồng tiếng video) | Tải video/link lên → tách giọng → nhận dạng lời → dịch → đọc lại bằng giọng nhân bản, khớp thời gian |
| **Transcription** | Chuyển giọng nói thành văn bản (WhisperX, Faster-Whisper, Parakeet…) |
| **Dictation** | Widget đọc chính tả nổi, bấm phím tắt là nói → ra chữ ở mọi ứng dụng |
| **Stories / Audiobook** | Đọc truyện, sách nói nhiều chương, xuất MP3/M4B |
| **Batch, Vocal isolation, Speaker diarization** | Xử lý hàng loạt, tách lời khỏi nhạc nền, phân biệt người nói |
| **Local API + MCP** | API tương thích OpenAI (`/v1/audio/speech`) và MCP server cho các AI agent |

Engine mặc định là **OmniVoice (k2-fsa)**, hỗ trợ 646 ngôn ngữ — **có tiếng Việt** (mã `vi`).
Ngoài ra có thể cài thêm các engine khác: CosyVoice 3, IndexTTS 2.5, KittenTTS, VoxCPM2,
Supertonic-3, PocketTTS… (xem [`docs/feature-catalog.md`](docs/feature-catalog.md)).

---

## 2. Cấu hình máy

| Phần cứng | Mức hỗ trợ |
|---|---|
| Card NVIDIA (Windows / Linux) | Tăng tốc CUDA — nhanh nhất |
| Mac chip Apple (M1/M2/M3/M4…) | Tăng tốc Metal (MPS) |
| Máy không có card rời (Intel/AMD tích hợp, máy cũ) | Chạy được bằng CPU, chậm hơn; cần khoảng 5 GB trống |
| Windows ARM (Snapdragon X…) | Thử nghiệm, chỉ CPU |
| Mac chip Intel | **Không chạy được phần xử lý AI**, chỉ dùng giao diện kết nối tới máy khác |

- Ổ cứng trống: khoảng **10 GB** (app + môi trường Python + model).
- Hệ điều hành: Windows 10/11 x64, macOS 13.3+, Linux x86_64 có giao diện đồ hoạ.
- Python, FFmpeg và model đều được app **tự tải ở lần chạy đầu** — bạn không cần cài trước.

---

## 3. Cài đặt

### Cách A — Cài bản đóng gói (dễ nhất, khuyên dùng)

Vào trang [Releases](https://github.com/debpalash/VoiceStudio/releases/latest) và tải file
phù hợp:

| Máy | File cần tải |
|---|---|
| Windows (Intel/AMD) | `VoiceStudio-Electron-<phiên bản>-win-x64.exe` |
| macOS chip Apple | `VoiceStudio-Electron-<phiên bản>-mac-arm64.dmg` |
| Linux (mọi bản) | `VoiceStudio-Electron-<phiên bản>-linux-x64.AppImage` |
| Ubuntu / Debian | `VoiceStudio-Electron-<phiên bản>-linux-x64.deb` |

Hoặc cài bằng một lệnh:

```powershell
# Windows — mở PowerShell thường (không cần quyền Admin)
irm https://voicestudio.sh/install | iex
```

```sh
# macOS / Linux
curl -fsSL https://voicestudio.sh/install | sh
```

Ghi chú theo hệ điều hành:

- **Windows:** chạy file `.exe`, mặc định cài cho user hiện tại, không cần Admin.
- **macOS:** mở file `.dmg`, kéo **VoiceStudio.app** vào `Applications`.
- **Linux AppImage:**
  ```bash
  chmod +x VoiceStudio-Electron-*-linux-x64.AppImage
  ./VoiceStudio-Electron-*-linux-x64.AppImage
  ```
- **Linux .deb:** `sudo apt install ./VoiceStudio-Electron-<phiên bản>-linux-x64.deb`

### Cách B — Chạy từ mã nguồn trong repo này

Dành cho người muốn sửa code hoặc chạy bản mới nhất.

**Cần cài trước:** Git, [Bun](https://bun.sh), [uv](https://docs.astral.sh/uv/),
Node.js 22+, Rust/Cargo, và bộ công cụ build C++:

- Windows: *Visual Studio 2022 Build Tools* với workload **"Desktop development with C++"**.
- Ubuntu/Debian:
  ```bash
  sudo apt-get install -y build-essential pkg-config libasound2-dev libxdo-dev \
    libxtst-dev libx11-dev libxkbcommon-dev libwayland-dev libssl-dev binutils zsync
  ```

**Chạy:**

```bash
git clone https://github.com/pichu124/ElevenLabs.git
cd ElevenLabs
bun install
bun run setup:api   # tạo môi trường Python bằng uv (lần đầu hơi lâu)
bun run dev         # mở app Electron; app tự khởi động backend ở cổng 3900
```

> Không cần mở backend riêng ở terminal khác — `bun run dev` tự lo.

Các lệnh khác:

| Lệnh | Tác dụng |
|---|---|
| `bun run desktop-prod` | Build bản production rồi chạy |
| `bun run dist` | Tạo file cài đặt (`.exe` / AppImage / `.deb`) trong `electron/release/` |
| `bun run dev:web` | Chạy backend + giao diện web (mở bằng trình duyệt) |

### Cách C — Docker (cho máy chủ, mở bằng trình duyệt)

```bash
# Tạo khoá API (bắt buộc với Docker)
export OMNIVOICE_API_KEY="$(python3 -c 'import secrets; print(secrets.token_urlsafe(32))')"

# Máy có GPU NVIDIA (cần NVIDIA Container Toolkit). Máy chỉ có CPU: bỏ "--gpus all"
docker run -d --name omnivoice --gpus all \
  -p 127.0.0.1:3900:3900 \
  -e OMNIVOICE_API_KEY="$OMNIVOICE_API_KEY" \
  -v omnivoice-data:/app/omnivoice_data \
  ghcr.io/debpalash/voicestudio:stable
```

Mở <http://localhost:3900>, nhập khoá API ở trên khi được hỏi. Lần đầu sẽ tải khoảng
2,4 GB model — xem tiến độ bằng `docker logs -f omnivoice`.
Chi tiết (GPU AMD/ROCm, docker compose…): [`docs/install/docker.md`](docs/install/docker.md).

---

## 4. Lần mở app đầu tiên

1. App hiện màn hình **setup**: tự phát hiện GPU, tải Python + PyTorch (bản CUDA nếu có
   card NVIDIA, bản CPU nếu không). Đợi xong.
2. Máy yếu / không có GPU: chọn preset **CPU** (engine nhẹ như KittenTTS, Supertonic-3,
   PocketTTS và model Whisper nhỏ).
3. **(Nên làm)** Tạo token Hugging Face tại <https://huggingface.co/settings/tokens>, rồi
   dán vào **Settings → API Keys** → bấm **Test now**. Token cần cho một số model lớn và
   tính năng phân biệt người nói.
4. Model chỉ được tải khi bạn bấm cài — app sẽ hỏi khi cần. Quản lý model ở
   **Settings → Models**.

---

## 5. Sử dụng các tính năng chính

### 5.1. Nhân bản giọng nói (Voice cloning)

1. Mở **Voice cloning** ở thanh bên trái.
2. Chọn một giọng có sẵn, **hoặc** thêm giọng mới bằng một đoạn ghi âm mẫu **sạch**
   (một người nói, không nhạc nền, không tiếng ồn). Có thể kèm lời thoại của đoạn mẫu
   để kết quả chính xác hơn.
3. Chọn ngôn ngữ **Vietnamese**, nhập văn bản cần đọc.
4. Bấm **Generate**. Nếu chưa có model, app sẽ hỏi cài → đồng ý.
5. Nghe thử, chỉnh tham số nếu cần, rồi tải file âm thanh về.

> Mẹo: chất lượng giọng nhân bản phụ thuộc nhiều nhất vào chất lượng file mẫu.
> Nếu đoạn mẫu có nhạc, dùng **Vocal isolation** để tách lời trước.

### 5.2. Thiết kế giọng mới (Voice design)

1. Mở **Voice design**.
2. Mô tả giọng bằng các thuộc tính, ví dụ: `female, young adult, high pitch`
   (giới tính, độ tuổi, cao độ, phong cách, giọng vùng).
3. Nghe thử → **Save** để lưu thành giọng dùng lại về sau.

### 5.3. Lồng tiếng video (Dub)

1. Mở **Dub**.
2. Kéo thả file video/audio, hoặc dán link video.
3. App tự tách giọng và nhận dạng lời thoại theo từng đoạn.
4. Chọn **ngôn ngữ đích** → **Translate** → đọc lại và sửa bản dịch từng dòng
   (có thể đổi giọng cho từng người nói).
5. Bấm **Generate** → xem trước → **Export** video đã lồng tiếng.

> Dịch dùng nhà cung cấp chọn trong **Settings → Models → Translation**
> (có thể dùng model dịch chạy offline hoặc LLM bên ngoài).

### 5.4. Chuyển giọng nói thành chữ & đọc chính tả

- **Transcriptions:** tải file âm thanh/video lên để lấy văn bản (có mốc thời gian).
- **Dictation:** đặt phím tắt trong **Settings → Hotkey**; nhấn phím → nói → chữ được
  chèn vào ứng dụng đang mở (trên Linux Wayland, chữ được copy vào clipboard).

### 5.5. Truyện & sách nói (Stories / Audiobook)

1. Mở **Stories** hoặc **Audiobook**.
2. Chọn giọng mặc định, dán hoặc import văn bản (dòng tiêu đề chương được nhận diện tự động).
3. **Stories** cho phép đổi giọng theo từng dòng (đọc nhiều nhân vật).
4. Bấm render → xuất MP3 hoặc M4B. Có preset âm lượng chuẩn ACX / podcast.

### 5.6. Dùng trên điện thoại / máy khác trong mạng LAN

Ở chân app, bấm nút **Local** → chọn **Network**. App hiện địa chỉ IP, mã QR và mã PIN
6 số. Dùng điện thoại quét QR là mở được giao diện. Bấm **Stop sharing** để tắt.
(Truy cập từ xa ngoài mạng nhà: dùng Tailscale — xem [`docs/sharing.md`](docs/sharing.md).)

---

## 6. Dùng qua API (cho lập trình viên)

Khi app đang chạy, backend lắng nghe ở `http://127.0.0.1:3900`. Tài liệu API trực quan:
<http://127.0.0.1:3900/docs>.

API tương thích OpenAI — dùng được ngay thư viện `openai`:

```bash
curl http://127.0.0.1:3900/v1/audio/speech \
  -H "Content-Type: application/json" \
  -d '{"model":"tts-1","voice":"alloy","input":"Xin chào, đây là VoiceStudio.","response_format":"wav"}' \
  --output speech.wav
```

```python
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:3900/v1", api_key="any")  # chạy local không cần key
audio = client.audio.speech.create(model="tts-1", voice="alloy",
                                   input="Xin chào, đây là VoiceStudio.")
audio.write_to_file("speech.mp3")
```

- Chuyển giọng nói thành chữ từ dòng lệnh: `python -m backend.speech_client transcribe recording.wav`
- Kết nối với Claude Code / các AI agent qua MCP: xem [`docs/mcp.md`](docs/mcp.md).
- Khi bật `OMNIVOICE_API_KEY` (ví dụ chạy Docker), thêm header
  `Authorization: Bearer <khoá>` — xem [`docs/api-auth.md`](docs/api-auth.md).

---

## 7. Cập nhật mã nguồn từ bản gốc

Repo này có thể kéo bản cập nhật mới nhất từ dự án gốc:

```bash
git remote add upstream https://github.com/debpalash/VoiceStudio.git   # chỉ làm 1 lần
git fetch upstream
git merge upstream/main
```

Nếu dùng bản cài đặt (Cách A) thì app tự báo khi có bản mới.

---

## 8. Lỗi thường gặp

| Vấn đề | Cách xử lý |
|---|---|
| Cửa sổ trắng / lỗi GPU trên Linux | Chạy `./VoiceStudio-...AppImage --disable-gpu` (Wayland: thêm `--ozone-platform=x11`) |
| Hết VRAM khi tải engine (CUDA out of memory) | **Settings → Performance** → bật **Disable torch.compile** |
| Card AMD trên Windows không được dùng | Bình thường — PyTorch trên Windows chỉ hỗ trợ NVIDIA; app chạy bằng CPU. Xem [`docs/install/windows.md`](docs/install/windows.md#gpu-support) |
| Tải model / thư viện quá chậm, bị chặn | Xem mục *Restricted networks* trong [`docs/install/linux.md`](docs/install/linux.md) |
| Không biết app đang dùng GPU hay CPU | **Settings → Performance → GPU acceleration**, hoặc **Settings → About → Run self-check** |

Danh sách đầy đủ: [`docs/install/troubleshooting.md`](docs/install/troubleshooting.md).
Hỏi đáp cộng đồng: [Discord VoiceStudio](https://discord.gg/bzQavDfVV9).

---

## 9. Bản quyền & sử dụng có trách nhiệm

- Mã nguồn theo giấy phép **AGPL-3.0** (xem [`LICENSE`](LICENSE),
  [`LICENSE-NOTICE.md`](LICENSE-NOTICE.md)). Được dùng miễn phí, kể cả mục đích thương mại;
  nhưng nếu **sửa code rồi cung cấp cho người khác dùng qua mạng** (ví dụ làm thành web
  dịch vụ), bạn phải công khai mã nguồn bản đã sửa theo cùng giấy phép.
- Trọng số model (model weights) có **giấy phép riêng** của từng model — kiểm tra trong
  Model Manager trước khi dùng cho mục đích thương mại.
- **Chỉ nhân bản giọng nói của người đã đồng ý.** Không dùng để giả mạo, lừa đảo.
