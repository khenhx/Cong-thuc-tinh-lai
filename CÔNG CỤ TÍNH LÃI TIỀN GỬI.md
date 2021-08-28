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

## Tính lãi suất tháng nào cũng gửi (for) (C1)
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

## Tính lãi suất tháng nào cũng gửi (for) (C2)
```php
  public function submitForm(array &$form, FormStateInterface $form_state) {
    // Todo
    $soTien = $form_state->getValue('so_tien');

    $str = str_replace( ',', '', $soTien);

    $kyhan = $form_state->getValue('ky_han');

    $laiVaVon = $this->tinhLaiSuatThangNaoCungGui($str, $form_state->getValue('lai_suat'), $kyhan);

    \Drupal::messenger()->addMessage('Tiền lãi và vốn là ' . number_format($laiVaVon) . ' VND');
  }

  public function tinhLaiThang($soTienGuiBanDau, $laiSuat, $kyhan) {
    return $soTienGuiBanDau * pow(1 + ($laiSuat/100/12), $kyhan);
  }

  public function tinhLaiSuatThangNaoCungGui($soTienGuiMoiThang, $laiSuat, $soThang) {
    $tongTien = 0;
    for ($i = 1; $i <= $soThang; $i++) {
      $temp = $this->tinhLaiThang($soTienGuiMoiThang, $laiSuat, $i);
      $tongTien = $tongTien + $temp;
    }
    return $tongTien;
  }
}
```

## Tính lãi suất tiền gửi từ tháng đến năm.
```php
  public function submitForm(array &$form, FormStateInterface $form_state) {
    // Todo
    $soTien = $form_state->getValue('so_tien');

    $str = str_replace( ',', '', $soTien);

    $kyhan = $form_state->getValue('ky_han');

    $laiVaVon = $this->tinhLaiNam($str, $form_state->getValue('lai_suatt'), $form_state->getValue('lai_suatn'), $kyhan);
    $lai = $this->laiNam($str, $form_state->getValue('lai_suatt'), $form_state->getValue('lai_suatn'), $kyhan);

    \Drupal::messenger()->addMessage('Tiền lãi và vốn là ' . number_format($laiVaVon) . ' VND');
    \Drupal::messenger()->addMessage('Tiền lãi là ' . number_format($lai) . ' VND');
  }

## Tính lãi suất tháng
  public function tinhLaiThang($soTienGuiBanDau, $laiSuat, $kyhan) {
    return $soTienGuiBanDau * pow(1 + ($laiSuat/100/12), $kyhan);
  }

  public function tinhLaiSuatThangNaoCungGui($soTienGuiMoiThang, $laiSuat, $soThang) {
    $tongTien = 0;
    for ($i = 1; $i <= $soThang; $i++) {
      $temp = $this->tinhLaiThang($soTienGuiMoiThang, $laiSuat, $i);
      $tongTien = $tongTien + $temp;
    }
    return $tongTien;
  }
## Tính cộng gộp cả lãi và vốn
  public function tinhLaiNam($soTienGuiMoiThang, $laiSuatThang, $laiSuatNam, $kyhan) {
    $tongTien = 0;
    for ($i = 1; $i <= $kyhan; $i++) {
      $soTienGuiHangNam = $this->tinhLaiSuatThangNaoCungGui($soTienGuiMoiThang, $laiSuatThang, 12);
      $temp = $soTienGuiHangNam * pow(1 + ($laiSuatNam /100), $i);
      $tongTien = $tongTien + $temp;
    }

    return $tongTien;
  }

## Tính lãi suất
  public function laiNam($soTienGuiMoiThang, $laiSuatThang, $laiSuatNam, $kyhan) {
    $lai = 0;
    for ($i = 1; $i <= $kyhan; $i++) {
      $soTienGuiHangNam = $this->tinhLaiSuatThangNaoCungGui($soTienGuiMoiThang, $laiSuatThang, 12);
      $laiVaVon = $soTienGuiHangNam * pow(1 + ($laiSuatNam/100), $i);
      $lai = $lai + ($laiVaVon - $soTienGuiHangNam);
    }

    return $lai;
  }
}
```


## Tính lãi vay ngân hàng để mua nhà
```php
  public function submitForm(array &$form, FormStateInterface $form_state) {
    // Todo
    $soTien = $form_state->getValue('so_tien');

    $str = str_replace( ',', '', $soTien);

    $kyhan = $form_state->getValue('ky_han');

    $tienlai = $this->tinhLaiNam($str, $form_state->getValue('lai_suat'), $kyhan);
    $tienlaivavon = $this->soTienTraHangThang($str, $form_state->getValue('lai_suat'), $kyhan);

    \Drupal::messenger()->addMessage('Tiền lãi là ' . number_format($tienlai) . ' VND');
    \Drupal::messenger()->addMessage('Tiền lãi và vốn phải đóng là ' . number_format($tienlaivavon) . ' VND');
  }
## Tiền lãi và vốn phải trả trong một tháng
  public function soTienTraHangThang($soTienGuiBanDau, $laiSuat, $kyhan) {
    for ($i = 1; $i <= $kyhan; $i++) {
      $tienlai = $this->tinhLaiNam($soTienGuiBanDau, $laiSuat, $kyhan);
      $tienlaivavon = ($tienlai * $kyhan + $soTienGuiBanDau) / 60;
    }
    return $tienlaivavon;
  }

## Tiền lãi trong một năm
  public function tinhLaiNam($soTienGuiBanDau, $laiSuat, $kyhan) {
    $tienlai = ($soTienGuiBanDau / 100) * $laiSuat;
    return $tienlai;
  }
```

## Tính lãi vay ngân hàng để mua nhà khi có vợ và nhân đôi nguồn thu nhập
```php
  /**
   * {@inheritdoc}
   */
  public function submitForm(array &$form, FormStateInterface $form_state) {
    $soTienGuiHangThangTemp = $form_state->getValue('so_tien_gui_hang_thang');
    $soTienGuiHangThang = str_replace( ',', '', $soTienGuiHangThangTemp);

    $laiSuatGuiMoiThang = $form_state->getValue('lai_suat_gui_moi_thang');

    $kyHanGui = $form_state->getValue('ky_han_gui');

    $laiSuatGuiMoiNam = $form_state->getValue('lai_suat_gui_moi_nam');

    $bankService = \Drupal::service('personal_finance.bank_services');
    $tongTienThuNhapCuaVo = 0;
    if ($form_state->getValue('tinh_trang_hon_nhan') == 'co_vo') {
      $soTienGuiHangThangCuaVoTemp = $form_state->getValue('thu_nhap_cua_vo');
      $soTienGuiHangThangCuaVo = str_replace( ',', '', $soTienGuiHangThangCuaVoTemp);
      $tongTienThuNhapCuaVo = $bankService->tinhLaiNam($soTienGuiHangThangCuaVo, $laiSuatGuiMoiThang, $laiSuatGuiMoiNam, $kyHanGui);
    }

    $tongSoTienVonVaLai = $bankService->tinhLaiNam($soTienGuiHangThang, $laiSuatGuiMoiThang, $laiSuatGuiMoiNam, $kyHanGui);

    $tongTienCuaVoVaCk = $tongSoTienVonVaLai + $tongTienThuNhapCuaVo;

    $chungCuTemp = $form_state->getValue('chung_cu');
    $chungCu = str_replace( ',', '', $chungCuTemp);

    $soTienVayMuaChungCu = $chungCu - $tongTienCuaVoVaCk;

    $nhaPhoTemp = $form_state->getValue('nha_pho');
    $nhaPho = str_replace( ',', '', $nhaPhoTemp);

    $soTienVayMuaNhaPho = $nhaPho - $tongTienCuaVoVaCk;

    $kyHanVay = $form_state->getValue('ky_han_vay');

    $laiSuatVayMoiNam = $form_state->getValue('lai_suat_vay_moi_nam');

    $tienlaiMuaNhaPho = $bankService->tinhLaiVayNam($soTienVayMuaNhaPho, $laiSuatVayMoiNam, $kyHanVay);

    $tongTienLaiVaVonPhaiDong = $bankService->soTienTraHangThang($soTienVayMuaNhaPho, $laiSuatVayMoiNam, $kyHanVay);

    \Drupal::messenger()->addMessage('Tiền lãi khi mua nhà phố là ' . number_format($tienlaiMuaNhaPho) . ' VND');
    \Drupal::messenger()->addMessage('Tiền lãi và vốn phải đóng khi mua nhà phố là ' . number_format($tongTienLaiVaVonPhaiDong) . ' VND');

    if (($soTienVayMuaChungCu) <= 0) {
      \Drupal::messenger()->addMessage('Đã đủ tiền mua nhà');
    }
    else {
      $tienlaiMuaNhaChungCu = $bankService->tinhLaiVayNam($soTienVayMuaChungCu, $laiSuatVayMoiNam, $kyHanVay);

      $tongTienLaiVaVonkhiMuaNhaChungCu = $bankService->soTienTraHangThang($soTienVayMuaChungCu, $laiSuatVayMoiNam, $kyHanVay);

      \Drupal::messenger()->addMessage('Tiền lãi khi mua chung cư là ' . number_format($tienlaiMuaNhaChungCu) . ' VND');
      \Drupal::messenger()->addMessage('Tiền lãi và vốn phải đóng khi mua chung cư là ' . number_format($tongTienLaiVaVonkhiMuaNhaChungCu) . ' VND');
    }
  }
```
