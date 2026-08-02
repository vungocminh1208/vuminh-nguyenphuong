# Nối thiệp vào Google Sheet — nhận RSVP & giữ Sổ lưu bút

Cài một lần, được ba thứ:

| | |
|---|---|
| **Danh sách khách** | Tự vào Google Sheet, tiện đếm đầu người sát ngày cưới |
| **Email ngay** | Tới cả nhà trai và nhà gái, Gmail điện thoại đẩy thông báo như tin nhắn |
| **Sổ lưu bút bền** | Lời chúc còn mãi sau khi tải lại trang, và **bạn duyệt trước khi cho hiện** |

Miễn phí, không cần server, không lộ khoá bí mật nào.

---

## Về Zalo — nói thẳng luôn

Zalo **không có cách nào miễn phí và nhẹ** để thiệp tự nhắn tin vào số của nhà trai / nhà gái. Muốn tự gửi thì phải dùng ZNS, mà cái đó cần Official Account doanh nghiệp có xác thực, **một server riêng để giữ khoá bí mật** (không thể đặt trong thiệp — ai xem mã nguồn trang cũng thấy), và trả phí từng tin, phải xin duyệt mẫu tin trước. Cho một đám cưới thì không đáng.

Thay vào đó thiệp đã có sẵn **hai nút "Nhắn Zalo"** (nhà gái / nhà trai) ở dưới biểu mẫu RSVP và trên từng thẻ địa điểm — khách bấm là mở thẳng cửa sổ chat. Số lấy từ trường `dienThoai` trong khối `③`, không cần cấu hình gì thêm.

---

## Bước 1 — Tạo Google Sheet

Tạo một Sheet mới. Trên thanh địa chỉ, copy đoạn ID ở giữa:

```
docs.google.com/spreadsheets/d/【ĐOẠN_NÀY_LÀ_ID】/edit
```

Không cần tự tạo dòng tiêu đề — script sẽ tự tạo ở lần gửi đầu tiên.

## Bước 2 — Dán code vào Apps Script

Trong Sheet: **Tiện ích mở rộng → Apps Script**. Xoá hết code có sẵn, dán toàn bộ đoạn dưới, rồi sửa **ba dòng đầu**.

```javascript
/* ══════════════════════════════════════════════════════════════════════
   THIỆP CƯỚI — Apps Script hứng phản hồi khách + trả lời chúc đã duyệt
   Dán toàn bộ file này vào Apps Script, sửa ID_SHEET và EMAIL_NHAN,
   rồi Deploy > New deployment > Web app (Execute as: Me,
   Who has access: Anyone).
   ══════════════════════════════════════════════════════════════════════ */

const ID_SHEET    = 'dán ID Sheet vào đây';
const EMAIL_NHAN  = 'nhatrai@gmail.com, nhagai@gmail.com';   // cách nhau bằng dấu phẩy
const DUYET_TRUOC = true;   // true = phải tự tay tick mới cho lời chúc lên thiệp

const TIEU_DE = ['Thời điểm', 'Họ tên', 'Điện thoại', 'Tham dự',
                 'Số người', 'Dự buổi', 'Lời chúc', 'Hiện lên thiệp'];

const GIAY_CACHE = 60;      // thiệp đọc lại Sheet mỗi 60 giây, xem doGet

const chuan_ = v => String(v == null ? '' : v).trim();

/* Tìm vị trí cột THEO TÊN. Nếu ai đổi tên trong TIEU_DE thì báo lỗi rõ
   ràng ngay lúc script nạp, thay vì để getRange(row, 0) ném lỗi khó
   hiểu ở tận đâu.                                                    */
function cot_(ten) {
  const i = TIEU_DE.indexOf(ten);
  if (i < 0) throw new Error('Không tìm thấy cột "' + ten + '" trong TIEU_DE. '
    + 'Nếu bạn vừa đổi tên cột thì sửa lại cho khớp.');
  return i + 1;
}
const COT_TEN      = cot_('Họ tên');
const COT_DUDU     = cot_('Tham dự');
const COT_LOI_CHUC = cot_('Lời chúc');
const COT_DUYET    = cot_('Hiện lên thiệp');

/* Endpoint công khai, ai có URL cũng POST được -> cắt bớt cho an toàn.
   Bỏ luôn xuống dòng: lọt vào subject email là vỡ header, lọt vào ô
   Sheet là vỡ layout.                                                */
const cat_ = (v, n) => {
  const s = chuan_(v).replace(/[\r\n]+/g, ' ');
  return s.length > n ? s.slice(0, n) + '…' : s;
};

function bat_(v) {
  return v === true || /^(true|x|có|co|1)$/i.test(chuan_(v));
}

/* ─── Sheet ────────────────────────────────────────────────────────── */

/* So khớp CẢ 8 cột, chuẩn hoá đều hai vế. Chỉ so cột cuối thì header bị
   đổi tên ở giữa sẽ lọt, và từ đó doGet đọc lệch cột mà không ai biết. */
function khopTieuDe_(sheet) {
  const soCot = TIEU_DE.length;
  if (sheet.getMaxColumns() < soCot) return false;

  const dong1 = sheet.getRange(1, 1, 1, soCot).getValues()[0].map(chuan_);
  for (let i = 0; i < soCot; i++) {
    if (dong1[i] !== chuan_(TIEU_DE[i])) return false;
  }
  return true;
}

/* Lấy Sheet, tự vá header nếu thiếu hoặc sai. Dùng cho đường GHI. */
function bang_() {
  const sheet = SpreadsheetApp.openById(ID_SHEET).getSheets()[0];
  if (khopTieuDe_(sheet)) return sheet;

  const soCot  = TIEU_DE.length;
  const dangCo = sheet.getMaxColumns();
  if (dangCo < soCot) sheet.insertColumnsAfter(dangCo, soCot - dangCo);

  // Dòng 1 là header hay là DỮ LIỆU THẬT của ai đó? Chỉ ghi đè khi chắc
  // chắn là header (trống, hoặc ô đầu đúng tên cột đầu). Còn lại thì chèn
  // dòng mới phía trên — không xoá dữ liệu của ai.
  const dong1    = sheet.getRange(1, 1, 1, soCot).getValues()[0].map(chuan_);
  const trong    = dong1.every(v => v === '');
  const laHeader = trong || dong1[0] === chuan_(TIEU_DE[0]);
  if (!laHeader) sheet.insertRowBefore(1);

  sheet.getRange(1, 1, 1, soCot).setValues([TIEU_DE]).setFontWeight('bold');
  sheet.setFrozenRows(1);
  sheet.setColumnWidth(COT_LOI_CHUC, 420);
  return sheet;
}

/* doGet là đường CHỈ ĐỌC nên không được sửa Sheet: bang_() có thể chèn
   dòng, ghi header, đổi độ rộng cột — để nó chạy ở đó thì mỗi lượt khách
   mở thiệp là một lần ghi. Header sai thì trả null, thiệp vẫn hiện bình
   thường (chỉ là chưa có lời chúc), sửa bằng cách chạy tay suaBangCu(). */
function bangChiDoc_() {
  const sheet = SpreadsheetApp.openById(ID_SHEET).getSheets()[0];
  return khopTieuDe_(sheet) ? sheet : null;
}

/* ═══ CHẠY TAY MỘT LẦN nếu Sheet đã có lời chúc từ trước ═══
   Bổ sung ô tick cho những dòng cũ chưa có.
   Cách chạy: trong Apps Script, chọn "suaBangCu" ở ô chọn hàm cạnh nút
   Run, rồi bấm Run. Chạy lại nhiều lần cũng không sao.               */
function suaBangCu() {
  const sheet = bang_();
  const n = sheet.getLastRow() - 1;
  if (n <= 0) { Logger.log('Sheet chưa có lời chúc nào.'); return; }

  const o  = sheet.getRange(2, COT_DUYET, n, 1);
  const cu = o.getValues().map(r => bat_(r[0]));   // bat_ vốn đã trả boolean
  o.insertCheckboxes();                            // thao tác này đặt hết về false
  o.setValues(cu.map(v => [v]));                   // nên phải ghi lại SAU

  CacheService.getScriptCache().remove('loichuc');

  Logger.log('Xong. ' + n + ' dòng, đang bật ' + cu.filter(Boolean).length
    + ' dòng. Chạy lại lần nữa con số này phải không đổi.');
}

/* ─── Khách bấm "Gửi xác nhận" ─────────────────────────────────────── */

function doPost(e) {
  const p = (e && e.parameter) || {};
  let sheet, hang, dong;

  // Khoá CHỈ bọc đoạn ghi Sheet. Hai khách bấm cùng lúc thì getLastRow()
  // có thể trả về dòng của người kia, làm ô tick gắn nhầm dòng.
  const khoa = LockService.getScriptLock();
  try {
    khoa.waitLock(20000);
  } catch (err) {
    return ContentService.createTextOutput('ban');   // thiệp hiện "bấm gửi lại"
  }

  try {
    sheet = bang_();
    hang = [
      new Date(),               // giờ MÁY CHỦ. Không tin giờ máy khách gửi lên:
                                // lệch múi giờ, giả mạo được, và lưu thành text
                                // thì Sheet không sort hay định dạng ngày được.
      cat_(p.ten,     100),
      cat_(p.dt,       30),
      cat_(p.dudu,     30),
      cat_(p.soluong,  30),
      cat_(p.buoi,     60),
      cat_(p.loichuc, 500),
      false                     // chỗ giữ chỗ, giá trị thật ghi ở dưới
    ];
    sheet.appendRow(hang);
    dong = sheet.getLastRow();

    // insertCheckboxes() ĐẶT LẠI ô thành false, nên phải chèn ô tick TRƯỚC
    // rồi mới ghi giá trị. Làm ngược lại thì DUYET_TRUOC = false sẽ im lặng
    // không có tác dụng — lời chúc vẫn phải tick tay.
    const oDuyet = sheet.getRange(dong, COT_DUYET);
    oDuyet.insertCheckboxes();
    oDuyet.setValue(!DUYET_TRUOC);

    // Apps Script gom lệnh ghi vào buffer, đẩy xuống server ở cuối lần chạy.
    // finally lại chạy TRƯỚC lúc đó -> nhả khoá khi dữ liệu còn treo, người
    // sau appendRow đè lên. Phải flush trước khi nhả.
    SpreadsheetApp.flush();
  } catch (err) {
    return ContentService.createTextOutput('loi: ' + err);
  } finally {
    khoa.releaseLock();
  }

  // Có dòng mới -> xoá cache để thiệp thấy ngay, không chờ hết 60 giây.
  // Chỉ có tác dụng khi DUYET_TRUOC = false; chế độ duyệt tay thì lời chúc
  // chưa được tick nên chưa lọt vào danh sách.
  CacheService.getScriptCache().remove('loichuc');

  // Gửi mail NGOÀI khoá. MailApp mất 1-3 giây; giữ khoá suốt thời gian đó
  // là bắt mọi khách khác xếp hàng chờ, đông người là timeout mất phản hồi.
  // Gmail cũng chỉ cho ~100 thư/ngày, nên bọc try để hết hạn mức cũng không
  // làm mất phản hồi đã ghi — chỉ ghi chú lại vào ô đầu dòng.
  try {
    const gio = Utilities.formatDate(hang[0], Session.getScriptTimeZone(),
                                     'HH:mm dd/MM/yyyy');
    MailApp.sendEmail({
      to: EMAIL_NHAN,
      subject: '[Thiệp cưới] ' + (hang[COT_TEN - 1] || 'Khách')
             + ' — ' + hang[COT_DUDU - 1],
      body: TIEU_DE.slice(0, COT_DUYET - 1)
              .map((t, i) => t.padEnd(12) + ': ' + (i === 0 ? gio : hang[i]))
              .join('\n')
          + '\n\n'
          + ((DUYET_TRUOC && hang[COT_LOI_CHUC - 1])
             ? 'Lời chúc này CHƯA hiện trên thiệp. Mở Sheet, tick ô "'
               + TIEU_DE[COT_DUYET - 1] + '" ở dòng ' + dong + '.'
             : '')
    });
  } catch (err) {
    sheet.getRange(dong, 1).setNote('Không gửi được email: ' + err);
  }

  return ContentService.createTextOutput('ok');
}

/* ─── Thiệp gọi lúc mở trang, lấy lời chúc đã duyệt ────────────────── */

/* CHỈ trả về tên và lời chúc. KHÔNG BAO GIỜ trả số điện thoại hay thông
   tin tham dự của khách ra ngoài.                                     */
function doGet(e) {
  // Mỗi khách mở thiệp là một lần đọc Sheet (1-2 giây). Vài trăm khách mở
  // đi mở lại thì vừa chậm vừa tốn quota. Cache 60 giây: lời chúc vừa duyệt
  // tay chậm hiện tối đa 1 phút, đám cưới thì không ai để ý.
  const cache = CacheService.getScriptCache();
  let json = cache.get('loichuc');

  if (!json) {
    const sheet = bangChiDoc_();
    const n  = sheet ? sheet.getLastRow() - 1 : 0;
    let   ds = [];

    if (n > 0) {
      ds = sheet.getRange(2, 1, n, TIEU_DE.length).getValues()
        .filter(h => bat_(h[COT_DUYET - 1]) && chuan_(h[COT_LOI_CHUC - 1]) !== '')
        .map(h => ({
          ten: chuan_(h[COT_TEN - 1]) || 'Một người bạn',
          loi: chuan_(h[COT_LOI_CHUC - 1])
        }))
        .reverse();                      // mới nhất lên đầu
    }

    json = JSON.stringify({ duyetTruoc: DUYET_TRUOC, loiChuc: ds });
    cache.put('loichuc', json, GIAY_CACHE);
  }

  // Thiệp mở bằng nháy đúp (file://) sẽ bị chặn CORS, khi đó nó gọi lại
  // kiểu JSONP. Lọc tên hàm cho sạch trước khi trả về.
  const cb = String((e && e.parameter && e.parameter.callback) || '').replace(/[^\w$]/g, '');
  if (cb) {
    return ContentService.createTextOutput(cb + '(' + json + ')')
             .setMimeType(ContentService.MimeType.JAVASCRIPT);
  }
  return ContentService.createTextOutput(json)
           .setMimeType(ContentService.MimeType.JSON);
}
```

## Bước 3 — Triển khai

**Triển khai → Bản triển khai mới**, chọn:

| Mục | Chọn |
|---|---|
| Loại | **Ứng dụng web** |
| Thực thi với tư cách | **Tôi** |
| Ai có quyền truy cập | **Bất kỳ ai** ← không chọn cái này thì khách gửi không được |

Google hỏi cấp quyền (ghi Sheet + gửi email) → cho phép. Gặp cảnh báo "ứng dụng chưa được xác minh" thì chọn **Nâng cao → Chuyển tới…**, vì đây là script của chính bạn.

> **Mỗi lần sửa code phải Triển khai lại** (Triển khai → Quản lý bản triển khai → biểu tượng bút chì → Phiên bản: Mới). Chỉ bấm Lưu là chưa có tác dụng — đây là chỗ hay nhầm nhất.

## Bước 4 — Dán vào thiệp

Copy đường dẫn ứng dụng web (dạng `https://script.google.com/macros/s/AKfy.../exec`), mở `index.html`, tìm khối **⑥ NHẬN PHẢN HỒI RSVP**:

```javascript
  urlGuiRSVP: 'https://script.google.com/macros/s/AKfy.../exec',
  taiLoiChuc: true,
```

## Bước 5 — Tự thử trước khi gửi khách

**Đừng bỏ qua bước này.** Sai một chữ trong đường dẫn là mất hết phản hồi mà không ai biết.

1. Mở thiệp, điền tên mình, viết một lời chúc, bấm Gửi.
2. Sheet phải có dòng mới. Email phải về.
3. Lời chúc hiện ngay trong Sổ lưu bút, có nhãn vàng **"chờ cô dâu chú rể duyệt"**.
4. Mở Sheet, **tick ô cột "Hiện lên thiệp"** ở dòng đó.
5. Tải lại thiệp → lời chúc giờ hiện như một tấm thiệp bình thường, không còn nhãn chờ.

---

## Bước 6 — Duyệt lời chúc

Cột **"Hiện lên thiệp"** là công tắc. Tick vào là lời chúc lên thiệp cho mọi khách xem, bỏ tick là ẩn đi.

Vài điều tiện lợi:

- **Sửa được trước khi duyệt.** Cứ sửa thẳng ô "Lời chúc" trong Sheet — chữa lỗi chính tả, bỏ câu không phù hợp, cắt bớt cho gọn. Thiệp lấy đúng nội dung sau khi sửa.
- **Duyệt hàng loạt:** chọn nhiều ô trong cột đó rồi gõ dấu cách để tick cùng lúc.
- **Không muốn duyệt** thì đặt `DUYET_TRUOC = false` rồi triển khai lại — mọi lời chúc lên thiệp ngay. **Cân nhắc kỹ:** thiệp thường được gửi qua Zalo, Facebook, ai có link cũng gửi được, nên rất dễ có người viết linh tinh.
- Lời chúc **chưa duyệt** thì tuyệt đối không hiện với khách khác — kể cả bỏ trống tên, nó vẫn nằm im trong Sheet.

---

## Riêng tư

`doGet` là đường dẫn công khai, ai có link cũng gọi được. Nên nó được viết để **chỉ trả về đúng hai thứ: tên và lời chúc**, mà chỉ với những dòng đã duyệt.

**Số điện thoại, tình trạng tham dự, số người đi cùng của khách không bao giờ ra khỏi Sheet.** Nếu sau này anh/chị tự sửa `doGet`, giữ nguyên nguyên tắc đó.

---

## Thiệp xử lý sự cố thế nào

Tôi đã thử từng tình huống bằng một máy chủ giả lập, không phải suy đoán:

| Tình huống | Thiệp làm gì |
|---|---|
| Chưa nối Sheet | Hiện 4 lời chúc mẫu ở mục ⑧, để xem trước |
| Tải bình thường | Hiện lời chúc thật đã duyệt, mới nhất lên đầu |
| Mở bằng nháy đúp (`file://`) — CORS chặn `fetch` | Tự chuyển sang JSONP, vẫn tải được |
| Trình duyệt quá cũ, không có `fetch` | Đi thẳng JSONP |
| Sheet lỗi / mất mạng | Để trống + "Chưa mở được sổ lưu bút, bạn thử tải lại trang". **Không hiện lời chúc mẫu**, vì khách sẽ tưởng đó là lời chúc thật của người khác |
| Chưa ai chúc | "Chưa có lời chúc nào. Bạn viết những dòng đầu tiên nhé." |
| Gửi RSVP lỗi đường truyền | Nút đổi thành "Gửi lại", mời khách nhắn Zalo, **không** giả vờ đã gửi thành công |

---

## Hạn mức cần biết

- **Email: 100 thư/ngày** với Gmail thường (Google Workspace được 1.500). Vượt thì email dừng nhưng **Sheet vẫn ghi đủ** — dòng đó sẽ có ghi chú nhỏ ở ô đầu. Với một đám cưới thì rất khó vượt.
- **Thời gian chạy script: 90 phút/ngày** với tài khoản thường, tương đương vài nghìn lượt mở thiệp mỗi ngày. Thoải mái.

## Nhận thông báo trên điện thoại

Cài **Gmail**, bật thông báo. Muốn gọn hơn: tạo bộ lọc theo tiêu đề `[Thiệp cưới]` gắn vào một nhãn riêng, rồi chỉ bật thông báo cho nhãn đó.

---

## Cách nhẹ hơn (nếu không muốn dính Google Sheet)

**FormSubmit.co** — không tạo gì, đổi một dòng:

```javascript
  urlGuiRSVP: 'https://formsubmit.co/ajax/email-cua-ban@gmail.com',
  taiLoiChuc: false,        // bắt buộc: FormSubmit không trả lời chúc về được
```

Lần gửi đầu, FormSubmit email cho bạn một link xác nhận — bấm vào rồi mới nhận được.

**Đánh đổi:** chỉ có email. **Không có bảng danh sách, và Sổ lưu bút không giữ được lời chúc** (quay về chỉ hiện trong phiên đang mở). Khách đông thì nên chịu khó cài Apps Script.
