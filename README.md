# Hướng Dẫn

### Bước Đầu Tiên: Truy cập vào https://shopee.vn/ bằng trình duyệt chrome hoặc khác bằng, yêu cầu sử dụng trên máy tính nhé 

### Bước 1: Nhấn Phím F12 để mở devtools, sau đó chuyển sang mục "Console"

### Bước 2: Dán Code phía dưới và nhấn enter, và đợi 1 lát sẽ hiện ra kết quả

##  Nếu có lỗi thì hãy gõ "allow pasting" vào console sau đó thực hiện lại bước 1 và 2 nhé !

<img src='image.png'>

```javascript
var tongDonHang = 0;
var tongTienTietKiem = 0;
var tongtienhang = 0;
var tongtienhangchuagiam = 0;
var tongSanPhamDaMua = 0;
var trangThaiDonHangConKhong = true;
var offset = 0;
var si = 20;
function report() {
    var orders = [];
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            if (this.responseText.includes('details_list')) {
                orders = JSON.parse(this.responseText)['data']['details_list'];
                tongDonHang += orders.length;
                trangThaiDonHangConKhong = orders.length >= si;
                orders.forEach(order => {
                    let t31 = order['info_card']['final_total'] / 100000;
                    tongtienhang += t31;
                    order['info_card']['order_list_cards'].forEach(item => {
                        item['product_info']['item_groups'].forEach(itemGroups => {
                            itemGroups['items'].forEach(data => {
                                let t5 = data["order_price"] / 100000;
                                tongSanPhamDaMua += data["amount"];
                                tongtienhangchuagiam += t5;
                            });
                        })
                    });
                });
            } else {
                trangThaiDonHangConKhong = false;
            }
            offset += si;
            if (trangThaiDonHangConKhong) {
                console.log('Đã thống kê được: ' + tongDonHang + ' đơn hàng. Đang lấy thêm dữ liệu....');
                report();
            } else {
                tongTienTietKiem = tongtienhangchuagiam - tongtienhang;
                var tongTienChiTieuX = pxgPrice(tongtienhang);
                console.log("================================");
                console.log("%c" + PXGCert(tongtienhang), "font-size:26px;");
                console.log("%c(1)Số tiền bạn ĐÃ ĐỐT vào Shopee là: " + "%c" + pxgPrice(tongtienhang) + " vnđ%c", "font-size: 20px;", "font-size: 26px; color:orange;font-weigth:700", "font-size: 20px;");
                console.log("================================");
                console.log("%c(2)Tổng đơn hàng đã giao: " + "%c" + pxgPrice(tongDonHang) + " đơn hàng", "font-size: 20px;", "font-size: 20px; color:green");
                console.log("%c(3)Số lượng sản phẩm đã đặt: " + "%c" + pxgPrice(tongSanPhamDaMua) + " sản phẩm", "font-size: 20px;", "font-size: 20px; color:#fc0000");
                console.log("%c(4)Tổng tiền TIẾT KIỆM được nhờ áp Mã giảm giá Shopee: " + "%c" + pxgPrice(tongTienTietKiem) + " vnđ", "font-size: 18px;", "font-size: 18px; color:green");
                console.log("%c💰TỔNG TIẾT KIỆM: " + "%c" + pxgPrice(tongTienTietKiem) + " vnđ", "font-size: 24px;", "font-size: 24px; color:orange;font-weigth:");
            }
        }
    };

    xhttp.open("GET", "https://shopee.vn/api/v4/order/get_order_list?list_type=3&offset=" + offset + "&limit=" + si, true);
    xhttp.send();
}
function PXGCert(pri) {
    if (pri <= 10000000) {
        return "HÊN QUÁ! BẠN CHƯA BỊ SHOPEE GÂY NGHIỆN 😍";
    } else if (pri > 10000000 && pri <= 50000000) {
        return "THÔI XONG! BẠN BẮT ĐẦU NGHIỆN SHOPEE RỒI 😂";
    } else if (pri > 50000000 && pri < 80000000) {
        return "ỐI GIỜI ƠI! BẠN LÀ CON NGHIỆN SHOPEE CHÍNH HIỆU 😱";
    } else {
        return "XÓA APP SHOPEE THÔI! BẠN NGHIỆN SHOPEE NẶNG QUÁ RỒI 😝";
    }
}
function pxgPrice(number, fixed = 0) {
    if (isNaN(number)) return 0;
    number = number.toFixed(fixed);
    let delimeter = ',';
    number += '';
    let rgx = /(\d+)(\d{3})/;
    while (rgx.test(number)) {
        number = number.replace(rgx, '$1' + delimeter + '$2');
    }
    return number;
}
report();
```
