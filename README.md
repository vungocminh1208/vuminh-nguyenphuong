# Thiệp cưới online — đưa lên mạng

Trang thiệp cưới tĩnh, một file HTML, không cần build, không cần server.

```
index.html          ← toàn bộ thiệp (HTML + CSS + JS trong một file)
wrangler.jsonc      ← cấu hình deploy cho Cloudflare
_headers            ← cấu hình cache — Cloudflare và Netlify đọc
_redirects          ← chặn file hướng dẫn — Cloudflare và Netlify đọc
.assetsignore       ← file không đưa lên web — Cloudflare đọc
vercel.json         ← cấu hình cache — Vercel đọc
.vercelignore       ← file không đưa lên web — Vercel đọc
anh-cuoi/           ← thả ảnh cưới vào đây
ma-qr/              ← thả mã QR chuyển khoản vào đây
HUONG-DAN.txt       ← ảnh cưới (A) · mã QR & tài khoản (B) · link bản đồ (C)
HUONG-DAN-RSVP.md   ← nối RSVP và Sổ lưu bút vào Google Sheet
README.md           ← file này: đẩy lên GitHub và go live
```

Hai thư mục có kèm file ghi chú ngắn (`THA-ANH-CUOI-VAO-DAY.txt`, `THA-MA-QR-VAO-DAY.txt`) chỉ để nhắc tên file cần thả vào — xoá được, không ảnh hưởng gì.

**Sửa nội dung thiệp ở đâu:** mở `index.html`, mọi thứ nằm trong khối cấu hình ①–⑧ ngay đầu thẻ `<script>`. Chi tiết từng khối xem `HUONG-DAN.txt`.

> **Nếu bạn đang dùng bản cũ tên `thiep-cuoi.html`:** đổi tên thành **`index.html`**.
> Mọi nền tảng hosting đều tìm `index.html` ở thư mục gốc. Để tên khác thì mở link ra
> sẽ ra lỗi 404, hoặc phải gửi khách link dài `.../thiep-cuoi.html`.

---

## Chọn nền tảng nào?

Đặc điểm của thiệp cưới khác website thường ở hai chỗ, và chúng quyết định lựa chọn:

**Lưu lượng dồn cục.** Bạn gửi link vào nhóm Zalo họ hàng lúc 8 giờ tối, vài trăm người mở gần như cùng lúc. Cả tháng im ắng, rồi dồn hết vào vài giờ.

**Khách gần như 100% ở Việt Nam, phần lớn dùng 4G.** Nên điều quyết định tốc độ không phải server mạnh cỡ nào, mà là **CDN có điểm phát ở Việt Nam hay không**. Đường truyền quốc tế từ Việt Nam hay bị ảnh hưởng khi cáp biển gặp sự cố — chuyện xảy ra khá thường xuyên.

### Bảng so sánh

| | **Cloudflare**<br><small>Pages hoặc Workers</small> | GitHub Pages | Netlify | Vercel (Hobby) |
|---|---|---|---|---|
| Băng thông miễn phí | **Không giới hạn** | 100 GB/tháng (giới hạn mềm) | 100 GB/tháng | 100 GB/tháng |
| Vượt hạn mức thì sao | — | Bị chặn tốc độ, có thể trả lỗi 429 | **Tạm dừng site tới đầu kỳ sau** | Chặn deploy, nội dung tĩnh vẫn sống |
| Điểm phát tại Việt Nam | **Hà Nội, TP.HCM, Đà Nẵng** | Không | Không | Không |
| Deploy từ repo **riêng tư** | **Được, miễn phí** | **Không** — cần GitHub Pro | Được | Được |
| Tên miền riêng + HTTPS | Miễn phí | Miễn phí | Miễn phí | Miễn phí |
| Số lần build/tháng | 500 | 10 build/giờ | 300 phút build | 6.000 phút build |

<small>Cloudflare đang gộp Pages vào Workers. Hai đường giống nhau về hạn mức, chỉ khác địa chỉ miễn phí: `.pages.dev` so với `.workers.dev`.</small>

### Kết luận: đây là HAI quyết định, không phải một

Chỗ này tôi viết lại sau khi thực tế deploy gặp vấn đề — bản đầu tiên của README chỉ nói tới quyết định thứ nhất, mà quyết định thứ hai mới là cái làm thiệp mở được hay không.

#### Quyết định 1 — Nền tảng

| Nếu bạn | Chọn |
|---|---|
| Mua tên miền riêng | **Cloudflare** — nhanh nhất cho khách Việt Nam nhờ có điểm phát trong nước |
| Dùng địa chỉ miễn phí | **Netlify** — địa chỉ hai tầng không kèm tên GitHub, và **đổi tên site được bất cứ lúc nào** |

#### Quyết định 2 — Địa chỉ web

> ### ⚠ ĐỌC MỤC 2.8 TRƯỚC KHI DEPLOY
>
> Các tên miền miễn phí `workers.dev`, `pages.dev`, `netlify.app`, `vercel.app` đều là **tên miền dùng chung cho hàng triệu trang** — trong đó có cả trang lừa đảo. Nhà mạng Việt Nam chặn DNS theo diện rộng, nên **cả tên miền đó có thể bị chặn**, kéo theo thiệp của bạn.
>
> Đây không phải giả thuyết. Trong lúc viết README này, `workers.dev` **đã bị chặn thật** trên một mạng gia đình ở Việt Nam: 4G vào được, wifi thì không. Nghĩa là khách mời dùng cùng nhà mạng cũng sẽ không vào được.
>
> **Tên miền riêng ~265 nghìn/năm là cách chắc chắn duy nhất.** Không mua thì bắt buộc phải kiểm tra cả ba nhà mạng và giữ link dự phòng — xem mục **2.8**.

#### Vì sao Cloudflare cho quyết định 1

Ba lý do, theo thứ tự quan trọng:

**1. Repo được để riêng tư.** Source này chứa **số tài khoản ngân hàng, số điện thoại, địa chỉ nhà** của cả hai gia đình. GitHub Pages bản miễn phí **chỉ chạy được với repo công khai** — nghĩa là mọi thông tin đó bị công khai, bị Google lập chỉ mục, và **nằm vĩnh viễn trong lịch sử Git** kể cả sau này bạn xoá đi. Cloudflare đọc được repo riêng tư mà vẫn miễn phí, cả đường Pages và đường Workers.

**2. Băng thông không giới hạn.** Netlify khi vượt 100 GB thì **tạm dừng site** tới đầu kỳ sau — và tạm dừng *tất cả* project trong tài khoản. Đúng lúc khách đang mở link mà thiệp chết là tình huống tệ nhất có thể. Cloudflare không đặt hạn mức băng thông cho tài sản tĩnh ở bất kỳ gói nào.

Nói cho công bằng: với một đám cưới thì 100 GB rất khó vượt. Thiệp khoảng 5 MB kể cả ảnh, tức là gần **20.000 lượt mở** mới hết 100 GB. Nhưng "khó vượt" không bằng "không có hạn mức".

**3. Nhanh nhất cho khách Việt Nam.** Cloudflare có data center ngay tại **Hà Nội, TP.HCM và Đà Nẵng**. Ba nền tảng còn lại phải đi ra Singapore hoặc Hong Kong.

**Lưu ý thành thật:** có điểm phát trong nước không đảm bảo 100% khách được phục vụ từ đó — tuỳ cách nhà mạng (Viettel, VNPT, FPT) kết nối, một số trường hợp vẫn đi qua Singapore hoặc Nhật. Nhưng ngay cả khi đó vẫn gần hơn nhiều so với đi Mỹ.

### Khi nào chọn GitHub Pages thay thế

Chọn nó nếu bạn **không đặt số tài khoản, số điện thoại, địa chỉ nhà** vào thiệp (chỉ mời suông, mừng cưới đưa tay), hoặc bạn có GitHub Pro. Ưu điểm là ít bước hơn: đẩy code lên là xong, không cần tài khoản thứ hai.

---

## Bước 1 — Đẩy source lên GitHub

Cần cài **Git** trước: <https://git-scm.com/downloads>

### 1.1 Tạo repo

Vào <https://github.com/new>

| Mục | Điền |
|---|---|
| Repository name | `thiep-cuoi` |
| Visibility | **Private** ← chọn cái này |
| Add a README file | **Đừng tick** (đã có README rồi) |

Bấm **Create repository**.

### 1.2 Đẩy lên

Mở Terminal (macOS) hoặc Git Bash (Windows), `cd` vào thư mục chứa `index.html`:

```bash
git init
git add .
git commit -m "Thiệp cưới"
git branch -M main
git remote add origin https://github.com/TEN-CUA-BAN/thiep-cuoi.git
git push -u origin main
```

Thay `TEN-CUA-BAN` bằng tên tài khoản GitHub. Lần đầu push, GitHub sẽ hỏi đăng nhập — dùng **Personal Access Token** thay cho mật khẩu (Settings → Developer settings → Personal access tokens → Generate new token, tick quyền `repo`).

Không quen dùng dòng lệnh thì cài **GitHub Desktop** (<https://desktop.github.com>), bấm *Add Local Repository* rồi *Publish repository* — nhớ **bỏ tick** "Keep this code private" thì mới thành public, còn muốn private thì **giữ nguyên tick**.

### 1.3 Đừng commit ảnh chưa nén

Ảnh chưa nén sẽ nằm trong lịch sử Git mãi mãi, xoá sau cũng không giảm dung lượng repo. **Nén ảnh xuống dưới 400 KB mỗi tấm trước khi commit** (xem mục A7 trong `HUONG-DAN.txt`).

---

## Bước 2 — Cách A: Cloudflare (khuyên dùng)

> ### ⚠ Cloudflare đang gộp Pages vào Workers
>
> Từ khoảng đầu 2026, Cloudflare dồn Pages vào Workers. Hệ quả:
>
> - **"Workers & Pages" không còn ở cấp trên cùng** — nó nằm trong mục **Compute**
> - **Nhiều tài khoản mới không còn tab "Pages"** nữa. Mở link `/pages` sẽ bị đẩy về Workers
>
> Không sao cả — Workers làm được đúng việc này, thậm chí là hướng Cloudflare khuyến khích cho web tĩnh mới. Hướng dẫn dưới đây đi theo đường Workers, chạy được cho cả hai loại tài khoản.

### 2.1 Tạo tài khoản

<https://dash.cloudflare.com/sign-up> — miễn phí, không cần thẻ.

### 2.2 Tìm đúng chỗ trong dashboard

Menu bên trái → mở mục **Compute** → chọn **Workers & Pages**.

Không thấy chữ "Compute" thì tìm biểu tượng hoặc mục tên **Workers**. Cách chắc nhất là dùng link trực tiếp:

```
https://dash.cloudflare.com/?to=/:account/workers-and-pages
```

### 2.3 Nối với repo GitHub

1. Bấm **Create** hoặc **Create application**
2. Tìm khu vực **Import a repository** (không phải "Start with a template")
3. Bấm **Connect GitHub**, cho phép Cloudflare đọc repo `thiep-cuoi`
4. Chọn repo đó

> Nếu tài khoản bạn **vẫn còn tab "Pages"** thì dùng cũng được, kết quả như nhau: tab **Pages** → **Connect to Git**. Địa chỉ web khi đó là `<tên>.pages.dev` thay vì `.workers.dev`.

### 2.4 Cấu hình — để trống gần hết

Thiệp là HTML tĩnh, **không có bước build**:

| Mục | Điền |
|---|---|
| Project / Worker name | `thiep-cuoi` — hoặc đổi cho đẹp, xem Bước 3 |
| Production branch | `main` |
| Framework preset | **None** |
| Build command | **để trống** |
| Deploy / output directory | **để trống** hoặc `/` |

Repo đã có sẵn file **`wrangler.jsonc`** khai báo đúng những thứ này, nên nếu màn hình không hỏi gì thì cứ bấm tiếp — Cloudflare tự đọc file đó.

Bấm **Save and Deploy**. Khoảng một phút sau thiệp lên sóng.

### 2.5 Địa chỉ web sẽ là gì

| Đường deploy | Địa chỉ miễn phí |
|---|---|
| Workers | `thiep-cuoi.<subdomain>.workers.dev` |
| Pages (nếu còn) | `thiep-cuoi.pages.dev` |

`<subdomain>` là tên riêng của tài khoản bạn, Cloudflare tự đặt lúc đầu nhưng **sửa được**: trong trang Workers & Pages, bấm **Change** cạnh *Your subdomain*.

Địa chỉ Workers có ba tầng nên dài hơn Pages một chút. Muốn gọn đẹp thật thì xem Bước 3.

### 2.6 Từ giờ trở đi

Mỗi lần `git push`, Cloudflare tự deploy lại. Không cần làm gì thêm.

### 2.7 Deploy xong mà mở link ra "can't reach this page"

Đây **không phải lỗi 404**. "Can't reach this page" nghĩa là trình duyệt còn chưa tìm được máy chủ — tức là vấn đề ở tầng DNS, chưa tới nội dung. Nội dung thiệp gần như chắc chắn vẫn ổn.

Làm theo thứ tự:

**1. Chờ. Có thể lâu hơn bạn tưởng.**
Địa chỉ `workers.dev` cấp lần đầu cần thời gian lan ra hệ thống DNS toàn cầu. Cloudflare hay ghi "vài phút", nhưng trên diễn đàn của họ có trường hợp mất tới **3 tiếng rưỡi**. Nếu vừa deploy xong thì cứ để đó, đi làm việc khác rồi thử lại.

**2. Kiểm tra đường workers.dev có đang bật.**
Vào Worker của bạn → **Settings** → **Domains & Routes**. Tìm dòng `workers.dev` — phải ở trạng thái bật. Nếu bị tắt thì địa chỉ đó không hoạt động.

**3. Thử bằng 4G thay vì wifi nhà.**
Đây là bước quan trọng nhất và mất 10 giây. Tắt wifi trên điện thoại, mở link bằng 4G.

- **4G vào được, wifi không** → nhà mạng của bạn đang chặn hoặc phân giải DNS sai. Đổi DNS trên máy sang `1.1.1.1` hoặc `8.8.8.8`.
- **Cả hai đều không vào** → chưa lan DNS xong, quay lại bước 1.

Đã có báo cáo thực tế trên diễn đàn công nghệ Việt Nam về việc **một loạt trang host trên Cloudflare không truy cập được qua mạng VNPT**, kể cả bằng Chrome và Firefox. Nguyên nhân liên quan tới cách nhà mạng xử lý ECH (mã hoá tên miền khi bắt tay TLS). Người dùng lẻ có thể tắt "DNS bảo mật" trong Chrome để lách, nhưng **bạn không thể yêu cầu hai trăm khách mời làm việc đó**.

**4. Đừng thử đổi subdomain của tài khoản để chữa.**
Xem mục cảnh báo ở Bước 3 — việc này hay thất bại và có thể làm mọi thứ chết thêm vài tiếng.

---

### 2.8 · 4G vào được nhưng wifi / LAN thì không

Đây là kết luận dứt điểm: **nhà mạng đang chặn tên miền, không phải lỗi deploy.** Thiệp vẫn đang chạy tốt.

**Điều nghiêm trọng là: khách mời dùng cùng nhà mạng cũng sẽ không vào được.** Mà anh/chị không thể yêu cầu hai trăm khách đổi DNS. Nên đây không còn là chuyện link đẹp hay xấu.

#### Hai phép thử để biết chữa cách nào

**Phép thử 1 — Có phải Cloudflare bị chặn toàn bộ?**

Vẫn dùng wifi nhà, mở thử một trang bình thường cũng nằm trên Cloudflare:

```
https://developers.cloudflare.com
```

| Kết quả | Nghĩa là | Cách chữa |
|---|---|---|
| **Vào được** | Nhà mạng chỉ chặn riêng tên miền `workers.dev`, không chặn Cloudflare | **Tên miền riêng chữa được.** Đi tiếp Bước 3 |
| Không vào được | Nhà mạng đang chặn diện rộng hơn | Xem "Phương án B" dưới đây |

**Phép thử 2 — Chặn bằng DNS hay bằng DPI?**

Đổi DNS trên máy tính sang `1.1.1.1` rồi thử lại:

*Windows:* Cài đặt → Mạng và Internet → chọn kết nối đang dùng → Chỉnh sửa DNS → Thủ công → bật IPv4 → DNS ưu tiên `1.1.1.1`, thay thế `8.8.8.8`.

| Kết quả | Nghĩa là |
|---|---|
| Vào được | Chặn bằng DNS (DNS poisoning). Đổi DNS là lách được — nhưng **chỉ cho máy của bạn**, khách vẫn không vào được |
| Vẫn không | Chặn bằng DPI, sâu hơn. Đổi DNS vô ích |

#### Bảng kết luận nhanh

| Test 1 | Test 2 | Chẩn đoán | Việc cần làm |
|---|---|---|---|
| Vào được | Vào được | Nhà mạng đầu độc DNS riêng cho `workers.dev`. Cloudflare bình thường | **Mua tên miền riêng.** Chắc chắn chữa được, không cần deploy lại |
| Vào được | Không | Chặn DPI theo tên miền `workers.dev` | **Mua tên miền riêng.** Vẫn chữa được vì DPI cũng lọc theo tên miền |
| Không | — | Nhà mạng chặn diện rộng hơn Cloudflare | Phương án B: hosting Việt Nam |

**Cả hai trường hợp đều dẫn tới cùng một kết luận: phải có tên miền riêng.** Vì cả DNS và DPI đều chặn **theo tên miền** — tên miền của riêng anh/chị không nằm trong danh sách đen nào cả.

#### Kiểm tra tên miền có bị chặn hay không

Có công cụ của người Việt test trực tiếp từ máy chủ đặt trong VNPT, Viettel, FPT: <https://vozdpi.website>

Nhập tên miền vào, nó cho biết từng nhà mạng có vào được không. **Nên test ngay sau khi mua tên miền, trước khi gửi thiệp cho khách.** Đây là công cụ của bên thứ ba, không phải của Cloudflare — nhưng rất hữu ích cho trường hợp này.

#### Không muốn mua tên miền? Cách miễn phí

Nhà mạng chặn **theo từng tên miền cụ thể**, không chặn Cloudflare. Nên chỉ cần đổi sang nền tảng có tên miền khác. Và mấy nền tảng dưới đây cho địa chỉ **hai tầng, không kèm tên GitHub** — sửa luôn cả chuyện link xấu.

| Nền tảng | Địa chỉ miễn phí | Đổi tên sau được? | Repo riêng tư |
|---|---|---|---|
| **Netlify** | `minhanh-quocbao.netlify.app` | **Được, bất cứ lúc nào** | Được |
| **Cloudflare Pages** | `minhanh-quocbao.pages.dev` | Không — phải xoá tạo lại | Được |
| **Vercel** | `minhanh-quocbao.vercel.app` | Được | Được |

<small>Gói Hobby của Vercel chỉ cho dùng **phi thương mại** — thiệp cưới cá nhân thì hoàn toàn hợp lệ.</small>
| GitHub Pages | `tenuser.github.io/thiep-cuoi/` | — | **Không** (cần Pro) |

GitHub Pages bị loại cho trường hợp này: địa chỉ **vẫn có tên user**, và bản miễn phí buộc repo phải công khai — mà repo có số tài khoản ngân hàng.

**Netlify là lựa chọn tốt nhất ở đây**, vì tên site đổi được bất cứ lúc nào. Cloudflare Pages thì `pages.dev` khoá vĩnh viễn như đã nói.

##### Cách thử nhanh nhất: Netlify, 60 giây, không cần Git

1. Vào <https://app.netlify.com/drop>
2. **Kéo thả cả thư mục** chứa `index.html` vào trang đó
3. Nó cho ngay một địa chỉ dạng `random-ten-123456.netlify.app`
4. **Mở địa chỉ đó bằng chính wifi nhà** — cái mạng vừa chặn `workers.dev`
   - Vào được → tìm ra đường rồi
   - Không vào được → thử tiếp Cloudflare Pages hoặc Vercel
5. Vào được thì đổi tên cho đẹp: **Site configuration → Site details → Change site name** → nhập `minhanh-quocbao`

Xong là có `minhanh-quocbao.netlify.app` — hai tầng, không tên GitHub, miễn phí.

Sau đó muốn tự deploy từ GitHub thì nối lại: **Add new site → Import an existing project → GitHub**.

##### Nếu chọn Vercel — cần đúng 3 thiết lập

Repo đã có sẵn `vercel.json` và `.vercelignore`, nên chỉ cần lo phần trong dashboard.

**Import repo:** vercel.com → **Add New** → **Project** → chọn repo `thiep-cuoi`.

Trong mục **Build and Output Settings**, đặt đúng ba thứ này:

| Mục | Đặt |
|---|---|
| Framework Preset | **Other** |
| Build Command | **để trống** |
| Output Directory | **để trống** (đừng điền `public`) |

Tài liệu Vercel ghi rõ: web tĩnh chỉ có HTML/CSS/JS thì không cần build, chọn preset **Other** và để trống ô build command.

Bấm **Deploy**. Xong là có `thiep-cuoi.vercel.app`. Đổi tên cho đẹp ở **Settings → General → Project Name**.

**Gặp lỗi 404 hoặc "No Output Directory named public found":** bật **Override** cho Output Directory rồi điền một dấu chấm `.` (nghĩa là thư mục gốc).

##### ⚠ File cấu hình của mỗi nền tảng KHÔNG dùng chung được

Đây là chỗ rất dễ bị lọt, vì **không nền tảng nào báo lỗi** — nó chỉ âm thầm bỏ qua file không phải của mình, và cấu hình cache của bạn coi như không tồn tại.

| Nền tảng | Cấu hình cache | Chặn file hướng dẫn |
|---|---|---|
| **Cloudflare** | `_headers` | `.assetsignore` (không upload) + `_redirects` |
| **Netlify** | `_headers` ← **dùng chung được** | `_redirects` (chuyển hướng) |
| **Vercel** | `vercel.json` | `.vercelignore` (không upload) |

**`_headers` và `_redirects` dùng chung giữa Netlify và Cloudflare được** — vì định dạng này vốn do Netlify tạo ra, Cloudflare sao chép lại để người dùng chuyển từ Netlify sang cho dễ. Cú pháp y hệt nhau.

Vercel thì không đọc hai file đó, nên phải có `vercel.json` riêng.

Cứ để cả 6 file trong repo — mỗi nền tảng tự lấy phần của mình, không đụng nhau. Deploy song song nhiều nơi cũng không sao.

**Riêng Netlify có một điểm yếu:** nó **không có file kiểu `.assetsignore` hay `.vercelignore`**. Với site không có bước build thì Netlify đưa mọi file trong repo lên web, kể cả `HUONG-DAN.txt`. Nên tôi dùng `_redirects` để chuyển hướng mấy đường dẫn đó về trang chính. Không sạch bằng cách không-upload-hẳn của hai nền tảng kia, nhưng đủ dùng — mấy file đó cũng không chứa gì bí mật, chỉ là không cần công khai.

Hai lưu ý riêng của Vercel:

- **`vercel.json` phải là JSON đúng chuẩn, KHÔNG được có comment** (khác `wrangler.jsonc` cho phép `//`). Thêm comment vào là deploy lỗi.
- **`vercel.json` phải được commit lên Git.** Nếu file nằm trong `.gitignore` thì Vercel không thấy và **không báo lỗi gì cả** — cấu hình chỉ đơn giản là không chạy.

##### ⚠ Đổi nền tảng thì phải sửa lại og:image

Dù chuyển sang Netlify, Vercel hay Pages, **địa chỉ web đổi thì `og:image` trong `<head>` cũng phải đổi theo**:

```html
<meta property="og:image" content="https://minhanh-quocbao.vercel.app/anh-cuoi/bia-anh-ngang.jpg">
```

Quên bước này thì gửi link Zalo sẽ không có ảnh xem trước. Nhân đó sửa luôn `<title>` và `og:title` nếu còn placeholder `{{...}}`.

##### ⚠ BẮT BUỘC: kiểm tra cả ba nhà mạng trước khi gửi khách

Mạng nhà bạn vào được **không có nghĩa là cả ba nhà mạng đều vào được.** Khách mời rải khắp Viettel, VNPT, FPT.

Vào <https://vozdpi.website>, nhập tên miền vừa chọn. Công cụ này test trực tiếp từ máy chủ đặt trong ba nhà mạng đó. Phải xanh cả ba mới yên tâm.

##### Rủi ro của cách miễn phí — nói thẳng

`netlify.app`, `pages.dev`, `vercel.app` đều là **tên miền dùng chung cho hàng triệu trang**, trong đó có cả trang lừa đảo. Nhà mạng chặn diện rộng theo tên miền, nên:

**Hôm nay chưa bị chặn không bảo đảm tuần sau vẫn thế.** Mà anh/chị thì không muốn phát hiện điều đó vào đúng hôm gửi thiệp, hoặc tệ hơn là vào hôm cưới.

Cách giảm rủi ro nếu quyết định không mua tên miền:

- **Deploy song song 2–3 nền tảng.** Đều miễn phí. Giữ sẵn link dự phòng, một cái chết thì gửi cái khác
- **Kiểm lại ở vozdpi.website vài ngày trước ngày cưới**
- Trong tin nhắn gửi khách, thêm một dòng: *"Nếu không mở được link, bạn thử bằng 4G nhé"* — vì 4G thường không bị chặn

Tên miền riêng ~265 nghìn cho một năm vẫn là cách chắc chắn nhất. Nhưng nếu không mua, làm ba việc trên là đã giảm rủi ro đi rất nhiều.

#### Phương án B — nếu Cloudflare bị chặn diện rộng ở nhà mạng của bạn

Thuê **hosting Việt Nam** (Nhân Hòa, Mắt Bão, Vietnix, AZDIGI…). Khoảng 100–200 nghìn một năm cho gói nhỏ nhất, đã quá đủ vì thiệp chỉ là file tĩnh.

**Ưu điểm cho riêng trường hợp này:** máy chủ đặt trong nước, khách không phải đi ra quốc tế, không phụ thuộc cáp biển, không lo bị chặn. Với đám cưới mà **100% khách ở Việt Nam** thì đây thật ra là lựa chọn ổn định nhất.

**Nhược điểm:** phải tự upload file qua FTP hoặc cPanel mỗi lần sửa, không tự deploy từ GitHub như Cloudflare.

Cách làm: mua hosting → vào File Manager của cPanel → upload toàn bộ (`index.html`, thư mục `anh-cuoi/`, `ma-qr/`) vào thư mục `public_html`. Xong.

> ### ⚠ Cloudflare tự nói: workers.dev không dành cho việc quan trọng
>
> Tài liệu chính thức ghi rõ địa chỉ `workers.dev` **được đối xử như một website miễn phí, dành cho dự án cá nhân hoặc thử nghiệm, không dành cho việc quan trọng**, và khuyến nghị chạy bản chính thức trên **tên miền riêng**.
>
> Thiệp cưới gửi cho hai trăm khách thì đúng là "việc quan trọng". Đây là lý do mạnh nhất để mua tên miền riêng — xem Bước 3.

## Bước 2 — Cách B: GitHub Pages

**Chỉ làm nếu bạn đã đọc phần "Riêng tư" ở dưới và repo là public.**

1. Repo → **Settings** → **Pages**
2. **Source**: Deploy from a branch
3. **Branch**: `main`, thư mục `/ (root)` → **Save**
4. Đợi 1–2 phút, link sẽ là `https://TEN-CUA-BAN.github.io/thiep-cuoi/`

Thêm một file rỗng tên **`.nojekyll`** ở thư mục gốc. GitHub Pages mặc định chạy Jekyll và **bỏ qua mọi file/thư mục bắt đầu bằng dấu gạch dưới** — thiệp này không có file như vậy, nhưng thêm `.nojekyll` cho chắc và deploy cũng nhanh hơn:

```bash
touch .nojekyll && git add .nojekyll && git commit -m "nojekyll" && git push
```

---

## Bước 3 — Tên miền cho đẹp

### Ba mức, chọn theo ngân sách

| Mức | Kết quả | Chi phí |
|---|---|---|
| **0** | `minhanh-quocbao.<subdomain>.workers.dev`<br>hoặc `minhanh-quocbao.pages.dev` | **Miễn phí** |
| **1** | `minhanh-quocbao.com` | ~265 nghìn / năm |
| **2** | `cuoi.tenmiencuaban.com` | Miễn phí nếu đã có tên miền |

---

### Mức 0 — Miễn phí, chỉ cần đặt tên project cho đẹp

Địa chỉ miễn phí lấy **đúng theo tên project** bạn đặt. Đặt `minhanh-quocbao` thì ra `minhanh-quocbao.<subdomain>.workers.dev` (hoặc `minhanh-quocbao.pages.dev` nếu đi đường Pages) — tình cảm hơn `thiep-cuoi` mà không mất đồng nào.

Sửa luôn dòng `"name"` trong `wrangler.jsonc` cho khớp:

```jsonc
"name": "minhanh-quocbao",
```

> ### ⚠ NGHĨ TÊN TRƯỚC KHI BẤM DEPLOY
>
> **Đường Pages:** địa chỉ `.pages.dev` **không đổi được sau khi tạo project**. Muốn đổi phải **xoá project và tạo lại** từ đầu.
>
> **Đường Workers:** đổi tên Worker thì địa chỉ đổi theo — linh hoạt hơn. Nhưng **link cũ chết ngay**, mà lúc đó nó đã nằm trong hàng chục nhóm Zalo.
>
> Cả hai đường đều nên nghĩ tên xong rồi mới deploy.

Vài mẫu tên: `minhanh-quocbao` · `quocbao-minhanh` · `cuoi-minhanh-quocbao` · `mavaqb`

Tên phải chưa ai dùng trên toàn Cloudflare, nên các tên chung chung như `thiepcuoi`, `wedding`, `cuoi` gần như chắc chắn đã hết. Ghép tên hai người thì hầu như luôn còn.

---

> ### ⚠ CÁI "TÊN USER GITHUB" TRONG LINK LÀ GÌ, VÀ ĐỪNG CỐ SỬA NÓ
>
> Địa chỉ Workers có dạng `<tên-worker>.<subdomain-tài-khoản>.workers.dev`.
>
> Phần `<subdomain-tài-khoản>` do **Cloudflare tự đặt một lần duy nhất** lúc bạn mở tài khoản, thường lấy theo email hoặc tên tài khoản GitHub nếu bạn đăng nhập bằng GitHub. Đó là lý do tên GitHub của bạn xuất hiện trong link.
>
> **Trên giấy tờ thì đổi được** (trang Workers & Pages → bấm **Change** cạnh *Your subdomain*). **Nhưng thực tế rất hay hỏng:**
>
> - Nhiều người báo lỗi `10036 — Account already has an associated subdomain`, tức là không cho đổi, phải mở ticket nhờ Cloudflare
> - Có trường hợp đổi xong thì **toàn bộ Worker chết, không vào được bằng cả tên cũ lẫn tên mới**, báo lỗi "DNS address could not be found", mất **3 tiếng rưỡi** mới sống lại
> - Vài người báo Cloudflare đã bỏ luôn link đổi trong dashboard
>
> **Bạn đang gặp lỗi DNS rồi, nên đừng đổi subdomain lúc này** — rủi ro làm mọi thứ tệ hơn mà không chắc được gì.
>
> **Ba cách sạch hơn, theo thứ tự khuyến nghị:**
>
> 1. **Mua tên miền riêng** (mục Mức 1 ngay dưới) — hết hẳn cả hai vấn đề, chắc chắn nhất
> 2. **Chuyển sang Netlify** — miễn phí, địa chỉ `minhanh-quocbao.netlify.app` hai tầng không tên GitHub, và **tên site đổi được bất cứ lúc nào** (khác Cloudflare). Xem mục 2.8
> 3. **Deploy lại bằng Cloudflare Pages** nếu tài khoản còn tab đó — `thiep-cuoi.pages.dev`, hai tầng, không tên GitHub. Nhưng `pages.dev` khoá vĩnh viễn, đặt tên sai là phải xoá tạo lại
>
> Cả cách 2 và 3 đều đổi được tên miền dùng chung sang cái khác, nên cũng có thể lách được việc `workers.dev` bị nhà mạng chặn.

---

### Mức 1 — Tên miền riêng, khoảng 265 nghìn một năm

Gửi `minhanh-quocbao.com` vào nhóm Zalo họ hàng trông trang trọng hơn hẳn `.pages.dev`, và cũng ngắn hơn để đọc qua điện thoại.

**Mua ở đâu**

*Cách 1 — Cloudflare Registrar* (dashboard → **Domain Registration** → **Register Domains**)

Bán **đúng giá gốc, không cộng lãi**: `.com` khoảng **$10.44/năm ≈ 265 nghìn**, và giá gia hạn **y như giá mua** — không có kiểu năm đầu rẻ năm sau đắt. Mua ngay tại đây thì Cloudflare tự cấu hình DNS, không phải làm gì thêm.

*Cần thẻ Visa/Mastercard thanh toán quốc tế.* Nếu chỉ có thẻ ATM nội địa thì dùng cách 2.

*Cách 2 — Nhà cung cấp Việt Nam* (Nhân Hòa, Mắt Bão, Vietnix, PA Việt Nam…)

Đắt hơn một chút (thường 300–400 nghìn cho `.com`) nhưng **trả được bằng chuyển khoản ngân hàng trong nước**, có hoá đơn, hỗ trợ tiếng Việt. Mua xong phải **đổi nameserver sang Cloudflare** — nhà cung cấp nào cũng có mục này, và Cloudflare sẽ chỉ rõ hai địa chỉ nameserver cần điền.

**Về đuôi tên miền**

`.com` là lựa chọn an toàn nhất: ai cũng quen, và người lớn trong họ không thấy lạ.

Có đuôi theo chủ đề như `.wedding`, `.love`, `.family` — đẹp nhưng thường đắt hơn `.com` khá nhiều. Xem giá thật tại <https://cfdomainpricing.com>.

`.vn` thì **không nên** cho việc này: đăng ký cần bản sao CMND/CCCD, thủ tục lâu hơn, giá cao hơn nhiều, và Cloudflare Registrar không bán `.vn`.

**Mẹo về chi phí:** tên miền cưới thực tế chỉ cần dùng khoảng nửa năm. Mua 1 năm là đủ, sau đó không gia hạn cũng được — **link `.pages.dev` vẫn sống mãi và miễn phí**, nên thiệp không bao giờ mất hẳn.

---

### Mức 2 — Đã có tên miền sẵn thì dùng subdomain

Có `tenmiencuaban.com` rồi thì tạo `cuoi.tenmiencuaban.com` — miễn phí, không mua gì thêm.

Trường hợp này **không cần chuyển nameserver sang Cloudflare**: chỉ cần thêm một bản ghi `CNAME` cho `cuoi` trỏ tới `minhanh-quocbao.pages.dev` ở chỗ đang quản lý DNS. Riêng tên miền gốc (không có subdomain) thì bắt buộc phải dùng nameserver Cloudflare.

---

### Gắn tên miền vào Cloudflare

**Đã deploy rồi thì KHÔNG phải deploy lại.** Chỉ gắn thêm tên miền vào project đang có.

**Đường Workers:**

1. Workers & Pages → chọn Worker của bạn
2. **Settings** → **Domains & Routes** → **Add** → **Custom domain**
3. Nhập tên miền, ví dụ `minhanh-quocbao.com`
4. Cloudflare kiểm tra DNS rồi tự cấp chứng chỉ HTTPS — thường vài phút, có khi tới 15 phút
5. Làm thêm một lần nữa cho `www.minhanh-quocbao.com`, để khách gõ kèm `www` cũng vào được

**Đường Pages:** project → tab **Custom domains** → **Set up a domain**, các bước còn lại như trên.

Mua tên miền ngay tại Cloudflare Registrar thì DNS được cấu hình tự động, không phải làm gì thêm. Mua ở nơi khác thì phải đổi nameserver sang Cloudflare trước (Cloudflare sẽ hiện rõ hai địa chỉ nameserver cần điền).

Xong rồi thì **địa chỉ `workers.dev` cũ vẫn chạy song song** — cứ để đó làm dự phòng, nhưng gửi khách bằng tên miền mới.

> **Lỗi 522 hay gặp:** thêm bản ghi CNAME ở DNS nhưng **quên khai báo tên miền trong tab Custom domains** của project. Cloudflare chưa biết tên miền đó thuộc về project nào nên trả lỗi. Phải làm cả hai bước.

---

### Sau khi gắn xong — ba việc bắt buộc

**1. Sửa `og:image` trong `<head>`** thành địa chỉ mới:

```html
<meta property="og:image" content="https://minhanh-quocbao.com/anh-cuoi/bia-anh-ngang.jpg">
```

Bỏ qua bước này thì ảnh xem trước khi gửi link Zalo sẽ mất. Đây là lỗi hay gặp nhất khi đổi tên miền.

**2. Gửi thử link mới cho chính mình qua Zalo**, xem khung chat có hiện đúng tên và ảnh không.

**3. Nhớ rằng link `.pages.dev` vẫn hoạt động song song.** Đó là dự phòng tốt — nhưng nếu muốn khách chỉ dùng tên miền đẹp, có thể chuyển hướng `.pages.dev` sang tên miền chính bằng **Bulk Redirects** trong Cloudflare.

## Bước 4 — Làm cho thiệp load nhanh

Theo thứ tự hiệu quả:

**1. Nén ảnh — quan trọng nhất.** Chín tấm ảnh máy ảnh chưa nén là gần 100 MB. Nén xuống dưới 400 KB mỗi tấm là tổng còn ~4 MB, nhanh hơn **25 lần**. Không có tối ưu nào khác bù được việc bỏ qua bước này. Dùng <https://squoosh.app>, lưu `.webp` càng tốt.

**2. Ảnh bìa phải nhẹ nhất.** Đó là ảnh khách thấy đầu tiên và nó tải ngay lập tức, không chờ cuộn. Để dưới 250 KB.

**3. Tự chứa font (nếu muốn kỹ).** Thiệp đang tải font từ Google Fonts, tức là thêm 2 lần bắt tay mạng ra server nước ngoài trước khi chữ hiện. Muốn bỏ hẳn: tải font về từ <https://gwfh.mranftl.com> (nhớ chọn bộ **vietnamese**), đặt vào thư mục `fonts/`, rồi thay thẻ `<link>` Google Fonts bằng khai báo `@font-face` trỏ vào file cục bộ. Giúp được khoảng 200–400 ms cho khách Việt Nam.

**4. Bản đồ chỉ tải khi khách cuộn tới.** Google Maps nhúng nặng vài MB; thiệp đã tự hoãn tải cho tới khi khách cuộn gần tới phần Thông tin lễ cưới. Ai chỉ xem trang bìa rồi thoát thì không tốn dữ liệu cho bản đồ. Bạn không phải cấu hình gì.

**5. File `_headers` đã có sẵn** — cho trình duyệt giữ ảnh lại 1 ngày, khách mở lại thiệp không phải tải lần nữa. Chạy được trên cả Cloudflare Pages, Cloudflare Workers và Netlify. GitHub Pages bỏ qua file này (vẫn không sao, CDN của GitHub tự cache).

**Không cần lo về số người vào cùng lúc.** Đây là trang tĩnh nằm trên CDN, không có database, không có server xử lý. CDN sinh ra để làm đúng việc này — vài nghìn người mở cùng lúc cũng không nặng hơn một người.

---

## Riêng tư — đọc trước khi chọn repo public

Thiệp này chứa:

- Số tài khoản ngân hàng của cô dâu, chú rể
- Số điện thoại hai bên gia đình
- Địa chỉ nhà riêng
- Ngày giờ chính xác cả hai gia đình đều không có ai ở nhà

Hai điều nên biết:

**Repo public thì nằm vĩnh viễn trong lịch sử Git.** Sau này xoá thông tin đi, người ta vẫn xem lại được commit cũ. Đây là lý do chính nên để repo **private** và dùng Cloudflare.

**Site nào cũng công khai, kể cả từ repo private.** Bất kỳ ai có link đều mở được — mà thiệp cưới thì được chuyển tiếp tự do trong các nhóm Zalo. Không tránh được, và cũng bình thường với thiệp cưới.

Nhưng bạn **có thể chặn Google lập chỉ mục**, để người lạ không tìm thấy thiệp khi tìm tên bạn. Thêm dòng này vào `<head>`:

```html
<meta name="robots" content="noindex, nofollow">
```

**Đánh đổi:** thiệp sẽ không xuất hiện trên Google. Ảnh xem trước khi gửi link Zalo/Facebook **vẫn hoạt động bình thường** (dùng thẻ `og:`, không liên quan tới lập chỉ mục). Với thiệp cưới thì phần lớn người sẽ chọn `noindex` — khách nhận link trực tiếp, chẳng ai đi tìm thiệp cưới trên Google.

---

## Checklist trước khi gửi link cho khách

Kiểm bằng **điện thoại thật, dùng 4G**, đừng chỉ kiểm trên máy tính.

**Ba mục đầu là quan trọng nhất** — chúng quyết định khách có mở được thiệp hay không:

- [ ] Mở link bằng **wifi nhà**, không chỉ 4G. Nhà mạng có thể chặn tên miền miễn phí
- [ ] Kiểm ở <https://vozdpi.website> — phải **xanh cả ba** Viettel, VNPT, FPT
- [ ] Nhờ 2–3 người khác nhà mạng mở thử link giúp

- [ ] File tên đúng `index.html`, mở link gốc ra thấy thiệp (không phải lỗi 404)
- [ ] Tên cô dâu chú rể đúng — kể cả **4 dòng trong `<head>`** (xem biển báo đầu file)
- [ ] Gửi thử link cho chính mình qua Zalo → khung chat hiện **đúng tên và có ảnh**, không hiện chữ `{{TEN_CO_DAU}}`
- [ ] Ảnh cưới hiện đủ, không ô nào còn hoạ tiết hoa văn
- [ ] Mã QR **không còn dòng chữ đỏ "MÃ MẪU — CHƯA QUÉT ĐƯỢC"**
- [ ] **Quét thử mã QR bằng app ngân hàng** → đúng số tài khoản, đúng tên chủ tài khoản
- [ ] Bấm nút "Sao chép số tài khoản" → dán ra kiểm tra, phải khớp với mã QR
- [ ] Gửi thử một phản hồi RSVP → Google Sheet có dòng mới, email về
- [ ] Bấm nút "Nhắn Zalo" → mở đúng số điện thoại
- [ ] Đã dán link Google Maps vào `linkBanDo` cả hai địa điểm (xem PHẦN C của `HUONG-DAN.txt`)
- [ ] Bản đồ **hiện ra bản đồ thật**, không phải chỉ hiện tên địa điểm + nút
- [ ] Bấm qua lại hai nút "Nhà gái" / "Nhà trai" → bản đồ có đổi
- [ ] Bấm "Xem chỉ đường" trên điện thoại → mở app Maps, ghim **đúng nhà**, không ghim ra giữa xã
- [ ] Đếm ngược chạy đúng ngày cưới
- [ ] Mở bằng 4G xem có phải chờ lâu không — nếu chậm thì ảnh chưa nén đủ

---

## Cập nhật về sau

```bash
git add .
git commit -m "Sửa ảnh cưới"
git push
```

Cloudflare tự deploy lại trong khoảng 1 phút. Sửa nội dung mà khách vẫn thấy bản cũ thì đợi 5 phút (theo cấu hình cache trong `_headers`), hoặc vào dashboard Cloudflare bấm **Purge cache**.

---

## Gặp lỗi thường gặp

| Hiện tượng | Nguyên nhân |
|---|---|
| **Dashboard Cloudflare không thấy "Workers & Pages"** | Nó nằm trong mục **Compute**, không còn ở cấp trên cùng. Hoặc dùng link trực tiếp `dash.cloudflare.com/?to=/:account/workers-and-pages` |
| **Không thấy tab "Pages" đâu cả** | Tài khoản mới không còn Pages nữa, Cloudflare đã gộp vào Workers. Dùng **Create application → Import a repository**, kết quả như nhau |
| **Mở link ra "can't reach this page"** | Lỗi DNS, không phải 404. Xem mục **2.7** — chờ DNS lan (có thể vài tiếng), kiểm tra `workers.dev` đang bật, và **thử bằng 4G** để loại trừ nhà mạng |
| **4G vào được, wifi / LAN thì không** | Nhà mạng chặn tên miền `workers.dev`. **Khách mời cùng nhà mạng cũng sẽ không vào được.** Xem mục **2.8** — cần tên miền riêng |
| **Link có tên user GitHub, xấu** | Đó là subdomain tài khoản, Cloudflare tự đặt lúc mở tài khoản. **Đừng cố đổi** — xem cảnh báo ở Bước 3. Mua tên miền riêng hoặc deploy lại bằng Pages |
| Mở link ra 404 | File không tên `index.html`, hoặc chưa nằm ở thư mục gốc |
| Ảnh không hiện, chỉ có hoạ tiết | Sai tên file, hoặc thư mục `anh-cuoi/` chưa được commit |
| Zalo hiện `{{TEN_CO_DAU}}` | Chưa sửa 4 dòng trong `<head>` |
| Zalo không hiện ảnh xem trước | `og:image` phải là địa chỉ **đầy đủ** `https://…`, không dùng đường dẫn tương đối |
| Zalo vẫn hiện thông tin cũ | Zalo/Facebook cache ảnh xem trước. Dùng Facebook Sharing Debugger để làm mới, hoặc thêm `?v=2` vào cuối link khi gửi |
| **Bản đồ chỉ hiện tên địa điểm + nút, không có bản đồ** | Đã dán link rút gọn `maps.app.goo.gl` — Google không cho nhúng loại này. Lấy link "Nhúng bản đồ" trên **máy tính**, xem PHẦN C của `HUONG-DAN.txt`. Bấm F12 xem Console, thiệp có nhắc |
| Bản đồ ghim lệch, ra giữa xã | `linkBanDo` còn để trống nên thiệp đang đoán vị trí từ chữ địa chỉ. Dán link Maps vào |
| Mã QR có dòng chữ đỏ "MÃ MẪU — CHƯA QUÉT ĐƯỢC" | Chưa thả file mã QR vào `ma-qr/`, hoặc sai tên file |
| Quét mã QR ra số khác với số hiện trên thiệp | `soTK` trong khối ⑤ và số nằm trong ảnh QR là hai thứ độc lập. Tạo lại mã QR cho khớp |
| Sổ lưu bút trống, quay mãi | Chưa dán URL Apps Script, hoặc chưa triển khai lại sau khi sửa code |
| Gửi RSVP báo lỗi đường truyền | Apps Script chưa đặt quyền "Bất kỳ ai" — xem `HUONG-DAN-RSVP.md` |
