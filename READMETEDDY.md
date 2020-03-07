Langkah-langkah untuk membuat REST Server adalah sebagai berikut :
1. Menginstall XAMPP atau WAMPP. Jika PC anda belum terinstall aplikasi tersebut, silahkan download terlebih dahulu dengan klik link di bawah ini:

-> untuk xampp : https://www.apachefriends.org/download.html
-> untuk wampp : http://www.wampserver.com/en/download-wampserver-64bits/

2. Kemudian install juga aplikasi postman dengan download melalui link berikut: https://www.postman.com/downloads/
3. lalu download CodeIgniter dan library server yang diperlukan melalui klik link di bawah (namun biasanya library REST sudah terinstall dalam CodeIgniternya):
https://github.com/ardisaurus/ci-restserver

Jika sudah terinstall library REST, coba jalankan http://127.0.0.1/rest_ci/index.php/rest_server dan akan muncul tampilan REST Server Test.
4. Selanjutnya membuat database dengan nama kontak. membuat table telepon dengan field id, nama, dan nomor. kemudian atur ulang di rest_ci/application/config/database.php dengan hostname = localhost, username = root, password = '' , database = kontak.

5. Kemudian membuat file baru kontak.php di application/controller dan masukkan kode program sebagai berikut:
```php
<?php

defined('BASEPATH') OR exit('No direct script access allowed');

require APPPATH . '/libraries/REST_Controller.php';
use Restserver\Libraries\REST_Controller;

class Kontak extends REST_Controller {

    function __construct($config = 'rest') {
        parent::__construct($config);
        $this->load->database(); 
    }

    //Menampilkan data kontak
    function index_get() { //fungsi untuk membaca data dari database
        $id = $this->get('id'); //memeriksa terdapat properti id atau tidak
        if ($id == '') { //bila tidak ada maka akan menampilkan keseluruhan data
            $kontak = $this->db->get('telepon')->result();
        } else { //bila adaproperti id, maka akan menampilkan data sesuai id yang diminta properti
            $this->db->where('id', $id);
            $kontak = $this->db->get('telepon')->result();
        }
        $this->response($kontak, 200);
    }

        //Mengirim atau menambah data kontak baru
        function index_post() { // fungsi untuk mengirim data baru dari client ke server 
            $data = array( //menerima data dan membuat data berupa array
                        'id'           => $this->post('id'),
                        'nama'          => $this->post('nama'),
                        'nomor'    => $this->post('nomor'));
            $insert = $this->db->insert('telepon', $data); //memasukkan data ke dalam database
            if ($insert) {
                $this->response($data, 200); //jika berhasil masukkan data, maka data masuk dalam arrat
            } else {
                $this->response(array('status' => 'fail', 502));
            }
        }
    
            //Memperbarui data kontak yang telah ada
    function index_put() { //fungsi untuk mengupdate atau edit data yang telah ada di server
        $id = $this->put('id'); //memanggil data berupa id
        $data = array( //menerima data dan memasukkan data ke dalam array
                    'id'       => $this->put('id'),
                    'nama'          => $this->put('nama'),
                    'nomor'    => $this->put('nomor'));
        $this->db->where('id', $id);
        $update = $this->db->update('telepon', $data); //menyimpan hasil perubahan yang dilakukan tadi
        if ($update) { 
            $this->response($data, 200); //jika berhasil, maka akan menampilkan data yang telah diupdate
        } else {
            $this->response(array('status' => 'fail', 502)); //jika gagal, maka tampil fail
        }
    }

        //Menghapus salah satu data kontak
        function index_delete() { //menghapus data yang telah ada di server
            $id = $this->delete('id'); //memanggil dan memilih data berdasarkan idnya
            $this->db->where('id', $id); //id sebagai target untuk menghapus sebuah data
            $delete = $this->db->delete('telepon');
            if ($delete) {
                $this->response(array('status' => 'success'), 201); //jika berhasil maka status success atau berhasil dan data berhasil dihapus
            } else {
                $this->response(array('status' => 'fail', 502)); //jika gagal, maka status gagal dan data tidak berhasil dihapus
            }
        }
}
?>
```
