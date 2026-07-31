# Thiệp cưới online — đưa lên mạng

Trang thiệp cưới tĩnh, một file HTML, không cần build, không cần server.

```
index.html          ← toàn bộ thiệp (HTML + CSS + JS trong một file)
_headers            ← cấu hình cache (chỉ Cloudflare Pages / Netlify đọc)
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

| | Cloudflare Pages | GitHub Pages | Netlify | Vercel (Hobby) |
|---|---|---|---|---|
| Băng thông miễn phí | **Không giới hạn** | 100 GB/tháng (giới hạn mềm) | 100 GB/tháng | 100 GB/tháng |
| Vượt hạn mức thì sao | — | Bị chặn tốc độ, có thể trả lỗi 429 | **Tạm dừng site tới đầu kỳ sau** | Chặn deploy, nội dung tĩnh vẫn sống |
| Điểm phát tại Việt Nam | **Hà Nội, TP.HCM, Đà Nẵng** | Không | Không | Không |
| Deploy từ repo **riêng tư** | **Được, miễn phí** | **Không** — cần GitHub Pro | Được | Được |
| Tên miền riêng + HTTPS | Miễn phí | Miễn phí | Miễn phí | Miễn phí |
| Số lần build/tháng | 500 | 10 build/giờ | 300 phút build | 6.000 phút build |

### Kết luận: dùng Cloudflare Pages

Ba lý do, theo thứ tự quan trọng:

**1. Repo được để riêng tư.** Source này chứa **số tài khoản ngân hàng, số điện thoại, địa chỉ nhà** của cả hai gia đình. GitHub Pages bản miễn phí **chỉ chạy được với repo công khai** — nghĩa là mọi thông tin đó bị công khai, bị Google lập chỉ mục, và **nằm vĩnh viễn trong lịch sử Git** kể cả sau này bạn xoá đi. Cloudflare Pages đọc được repo riêng tư mà vẫn miễn phí.

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

## Bước 2 — Cách A: Cloudflare Pages (khuyên dùng)

### 2.1 Tạo tài khoản

<https://dash.cloudflare.com/sign-up> — miễn phí, không cần thẻ.

### 2.2 Nối với repo

1. Trong dashboard: **Workers & Pages** → **Create** → tab **Pages** → **Connect to Git**
2. Bấm **Connect GitHub**, cho phép Cloudflare truy cập repo `thiep-cuoi`
3. Chọn repo đó → **Begin setup**

### 2.3 Cấu hình build

Thiệp là HTML tĩnh, **không có bước build**, nên để trống hết:

| Mục | Điền |
|---|---|
| Project name | `thiep-cuoi` (sẽ thành `thiep-cuoi.pages.dev`) |
| Production branch | `main` |
| Framework preset | **None** |
| Build command | **để trống** |
| Build output directory | **để trống** hoặc `/` |

Bấm **Save and Deploy**. Khoảng 1 phút sau thiệp lên sóng tại:

```
https://thiep-cuoi.pages.dev
```

### 2.4 Từ giờ trở đi

Mỗi lần `git push`, Cloudflare tự deploy lại. Không cần làm gì thêm.

---

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
| **0** | `minhanh-quocbao.pages.dev` | **Miễn phí** |
| **1** | `minhanh-quocbao.com` | ~265 nghìn / năm |
| **2** | `cuoi.tenmiencuaban.com` | Miễn phí nếu đã có tên miền |

---

### Mức 0 — Miễn phí, chỉ cần đặt tên project cho đẹp

Tên miền `.pages.dev` lấy **đúng theo tên project** bạn đặt lúc tạo. Đặt project là `thiep-cuoi` thì ra `thiep-cuoi.pages.dev`; đặt là `minhanh-quocbao` thì ra `minhanh-quocbao.pages.dev` — nhìn đã tình cảm hơn nhiều mà không mất đồng nào.

> ### ⚠ ĐẶT TÊN ĐÚNG NGAY LẦN ĐẦU
>
> Tên miền `.pages.dev` **không đổi được sau khi tạo project**. Muốn đổi thì phải **xoá project và tạo lại** từ đầu.
>
> Nên nghĩ tên trước khi bấm *Save and Deploy*. Nếu đã gửi link cho khách rồi mới muốn đổi thì coi như link cũ chết — mà link đã nằm trong hàng chục nhóm Zalo.

Vài mẫu tên: `minhanh-quocbao` · `quocbao-minhanh` · `cuoi-minhanh-quocbao` · `mavaqb`

Tên phải chưa ai dùng trên toàn Cloudflare, nên các tên chung chung như `thiepcuoi`, `wedding`, `cuoi` gần như chắc chắn đã hết. Ghép tên hai người thì hầu như luôn còn.

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

### Gắn tên miền vào Cloudflare Pages

1. Vào project → tab **Custom domains** → **Set up a custom domain**
2. Nhập tên miền, ví dụ `minhanh-quocbao.com`
3. Cloudflare kiểm tra DNS rồi tự cấp chứng chỉ HTTPS — thường vài phút, có khi tới 15 phút
4. Làm thêm một lần nữa cho `www.minhanh-quocbao.com`, để khách gõ kèm `www` cũng vào được

> **Lỗi 522 hay gặp:** thêm bản ghi CNAME ở DNS nhưng **quên khai báo tên miền trong tab Custom domains** của project. Pages chưa biết tên miền đó thuộc về nó nên trả lỗi. Phải làm cả hai bước.

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

**5. File `_headers` đã có sẵn** — cho trình duyệt giữ ảnh lại 1 ngày, khách mở lại thiệp không phải tải lần nữa. Chỉ Cloudflare Pages và Netlify đọc file này.

**Không cần lo về số người vào cùng lúc.** Đây là trang tĩnh nằm trên CDN, không có database, không có server xử lý. CDN sinh ra để làm đúng việc này — vài nghìn người mở cùng lúc cũng không nặng hơn một người.

---

## Riêng tư — đọc trước khi chọn repo public

Thiệp này chứa:

- Số tài khoản ngân hàng của cô dâu, chú rể
- Số điện thoại hai bên gia đình
- Địa chỉ nhà riêng
- Ngày giờ chính xác cả hai gia đình đều không có ai ở nhà

Hai điều nên biết:

**Repo public thì nằm vĩnh viễn trong lịch sử Git.** Sau này xoá thông tin đi, người ta vẫn xem lại được commit cũ. Đây là lý do chính nên để repo **private** và dùng Cloudflare Pages.

**Site nào cũng công khai, kể cả từ repo private.** Bất kỳ ai có link đều mở được — mà thiệp cưới thì được chuyển tiếp tự do trong các nhóm Zalo. Không tránh được, và cũng bình thường với thiệp cưới.

Nhưng bạn **có thể chặn Google lập chỉ mục**, để người lạ không tìm thấy thiệp khi tìm tên bạn. Thêm dòng này vào `<head>`:

```html
<meta name="robots" content="noindex, nofollow">
```

**Đánh đổi:** thiệp sẽ không xuất hiện trên Google. Ảnh xem trước khi gửi link Zalo/Facebook **vẫn hoạt động bình thường** (dùng thẻ `og:`, không liên quan tới lập chỉ mục). Với thiệp cưới thì phần lớn người sẽ chọn `noindex` — khách nhận link trực tiếp, chẳng ai đi tìm thiệp cưới trên Google.

---

## Checklist trước khi gửi link cho khách

Kiểm bằng **điện thoại thật, dùng 4G**, đừng chỉ kiểm trên máy tính.

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

Cloudflare Pages tự deploy lại trong khoảng 1 phút. Sửa nội dung mà khách vẫn thấy bản cũ thì đợi 5 phút (theo cấu hình cache trong `_headers`), hoặc vào dashboard Cloudflare bấm **Purge cache**.

---

## Gặp lỗi thường gặp

| Hiện tượng | Nguyên nhân |
|---|---|
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
