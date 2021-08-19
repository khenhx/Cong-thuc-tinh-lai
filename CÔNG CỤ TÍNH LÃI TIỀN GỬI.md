```php
(function($, Drupal) {
  'use strict';
  Drupal.behaviors.Pf = {
    attach: function(context) {
      function textthaydoi() {
        ## Lấy kết quả vào
        var value = $(this).val();
        
        ## Hàm xóa tất cả các dấu chấm phẩy
        var convertInt = value.replace(/,/g, '');
        
        ## Hàm tính để đặt dấu phẩy đúng chỗ
        var valueTemp = Number(convertInt).toLocaleString('en');
        $(this).val(valueTemp);
      }

       ## Trả về kết quả 
      $("#edit-deposits").keyup(textthaydoi);
    }
  };

})(jQuery, Drupal);
```
