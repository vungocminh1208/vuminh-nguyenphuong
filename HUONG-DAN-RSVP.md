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
const ID_SHEET    = 'dán ID Sheet vào đây';
const EMAIL_NHAN  = 'nhatrai@gmail.com, nhagai@gmail.com';   // cách nhau bằng dấu phẩy
const DUYET_TRUOC = true;   // true = phải tự tay tick mới cho lời chúc lên thiệp

const TIEU_DE = ['Thời điểm', 'Họ tên', 'Điện thoại', 'Tham dự',
                 'Số người', 'Dự buổi', 'Lời chúc', 'Hiện lên thiệp'];

function bang_() {
  const sheet = SpreadsheetApp.openById(ID_SHEET).getSheets()[0];
  if (sheet.getLastRow() === 0) {
    sheet.appendRow(TIEU_DE);
    sheet.getRange(1, 1, 1, TIEU_DE.length).setFontWeight('bold');
    sheet.setFrozenRows(1);
    sheet.setColumnWidth(7, 420);
  }
  return sheet;
}

function bat_(v) {
  return v === true || /^(true|x|có|co|1)$/i.test(String(v).trim());
}

/* Khách bấm "Gửi xác nhận" */
function doPost(e) {
  const p = (e && e.parameter) || {};
  const sheet = bang_();

  // Ghi vào Sheet TRƯỚC. Dù email có lỗi thì phản hồi của khách vẫn còn.
  sheet.appendRow([
    p.ngayGui || new Date(),
    p.ten     || '',
    p.dt      || '',
    p.dudu    || '',
    p.soluong || '',
    p.buoi    || '',
    p.loichuc || '',
    !DUYET_TRUOC
  ]);
  sheet.getRange(sheet.getLastRow(), 8).insertCheckboxes();

  // Gmail thường chỉ cho gửi 100 thư/ngày. Bọc lại để hết hạn mức
  // cũng không làm mất phản hồi — chỉ ghi chú lại vào ô đầu dòng.
  try {
    MailApp.sendEmail({
      to: EMAIL_NHAN,
      subject: '[Thiệp cưới] ' + (p.ten || 'Khách') + ' — ' + (p.dudu || ''),
      body: [
        'Họ tên     : ' + (p.ten     || ''),
        'Điện thoại : ' + (p.dt      || ''),
        'Tham dự    : ' + (p.dudu    || ''),
        'Số người   : ' + (p.soluong || ''),
        'Dự buổi    : ' + (p.buoi    || ''),
        'Lời chúc   : ' + (p.loichuc || ''),
        '',
        'Gửi lúc    : ' + (p.ngayGui || ''),
        (DUYET_TRUOC && p.loichuc)
          ? 'Lời chúc này CHƯA hiện trên thiệp. Mở Sheet, tick ô "Hiện lên thiệp" ở dòng cuối.'
          : ''
      ].join('\n')
    });
  } catch (err) {
    sheet.getRange(sheet.getLastRow(), 1).setNote('Không gửi được email: ' + err);
  }

  return ContentService.createTextOutput('ok');
}

/* Thiệp gọi hàm này lúc mở trang, để lấy lời chúc đã duyệt.
   CHỈ trả về tên và lời chúc. KHÔNG BAO GIỜ trả số điện thoại
   hay thông tin tham dự của khách ra ngoài.                    */
function doGet(e) {
  const sheet = bang_();
  const n = sheet.getLastRow() - 1;
  let ds = [];

  if (n > 0) {
    ds = sheet.getRange(2, 1, n, 8).getValues()
      .filter(function (h) { return bat_(h[7]) && String(h[6]).trim() !== ''; })
      .map(function (h) {
        return { ten: String(h[1]).trim() || 'Một người bạn',
                 loi: String(h[6]).trim() };
      })
      .reverse();                        // mới nhất lên đầu
  }

  const json = JSON.stringify({ duyetTruoc: DUYET_TRUOC, loiChuc: ds });

  // Thiệp mở bằng nháy đúp (file://) sẽ bị chặn CORS, khi đó nó
  // gọi lại kiểu JSONP. Lọc tên hàm cho sạch trước khi trả về.
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
