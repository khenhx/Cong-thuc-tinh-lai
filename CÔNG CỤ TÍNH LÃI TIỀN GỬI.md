## CÔNG CỤ TÍNH LÃI TIỀN GỬI
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

## Công thức tính Lãi kép (lãi cộng dồn) Ngắn gọn
```php
  public function submitForm(array &$form, FormStateInterface $form_state) {
    // Todo
    $soTien = $form_state->getValue('so_tien');

    $str = str_replace( ',', '', $soTien);

    $kyhan = $form_state->getValue('ky_han');

    $laiVaVon = $this->tinhLaiThang($str, $form_state->getValue('lai_suat'), $kyhan);

    \Drupal::messenger()->addMessage('Tiền lãi và vốn là ' . number_format($laiVaVon) . ' VND');
  }
  
  ## Công thức đây nha ngắn gọn
  public function tinhLaiThang($soTienGuiBanDau, $laiSuat, $kyhan) {
    return $soTienGuiBanDau * pow(1 + ($laiSuat/100/12), $kyhan);
  }
```

## Công thức tính Lãi kép (lãi cộng dồn) sử dụng vòng lắp for
```php
  public function submitForm(array &$form, FormStateInterface $form_state) {
    // Todo
    $soTien = $form_state->getValue('so_tien');

    $str = str_replace( ',', '', $soTien);

    $kyhan = $form_state->getValue('ky_han');

    $laiVaVon = $this->tinhLaiThang($str, $form_state->getValue('lai_suat'), $kyhan);

    \Drupal::messenger()->addMessage('Tiền lãi và vốn là ' . number_format($laiVaVon) . ' VND');
  }
  
  ## Này sử dụng vòng lặp for nên hơi rối
  public function tinhLaiThang($soTienGuiBanDau, $laiSuat, $kyhan) {
    for ($i = 0; $i < $kyhan; $i++) {
      $tienLai = round($soTienGuiBanDau / 100 * $laiSuat / 12);
      $tongLaiVaVon = $soTienGuiBanDau + $tienLai;
      $soTienGuiBanDau = $tongLaiVaVon;
    }
    return $soTienGuiBanDau;
  }
```

## Tính lãi suất tháng nào cũng gửi (for)
```php
  public function submitForm(array &$form, FormStateInterface $form_state) {
    // Todo
    $soTien = $form_state->getValue('so_tien');

    $str = str_replace( ',', '', $soTien);

    $kyhan = $form_state->getValue('ky_han');

    $laiVaVon = $this->tinhLaiSuatThangNaoCungGui($str, $form_state->getValue('lai_suat'), $kyhan);

    \Drupal::messenger()->addMessage('Tiền lãi và vốn là ' . number_format($laiVaVon) . ' VND');
  }
  
  ## Công thức nha
  public function tinhLaiThang($soTienGuiBanDau, $laiSuat, $kyhan) {
    $tongTien = 0;
    for ($i = 1; $i <= $kyhan; $i++) {
      $temp = $soTienGuiBanDau * pow(1 + ($laiSuat/100/12), $i);
      $tongTien = $tongTien + $temp;
    }

    return $tongTien;
  }
```
