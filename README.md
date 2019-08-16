# komen-likes

  
const  Client  =  membutuhkan ( ' instagram-private-api ' ). V1 ;
const  delay  =  membutuhkan ( ' delay ' );
const  chalk  =  membutuhkan ( ' chalk ' );
const  _  =  membutuhkan ( ' lodash ' );
const  rp  =  membutuhkan ( ' permintaan-janji ' );
const  S  =  membutuhkan ( ' string ' );
 inquirer  const =  membutuhkan ( ' inquirer ' );
var fs =  butuhkan ( ' fs ' ),
    request =  require ( ' request ' );

 pertanyaan  const = [
{
  ketik : ' input ' ,
  nama : ' nama pengguna ' ,
  message : ' [>] Masukkan Nama Pengguna: ' ,
  validasi :  function ( value ) {
    jika ( ! value) kembali  ' Bisa \' t Kosong ' ;
    kembali  benar ;
  }
},
{
  ketik : ' kata sandi ' ,
  nama : ' kata sandi ' ,
  pesan : ' [>] Masukkan Kata Sandi: ' ,
  topeng : ' * ' ,
  validasi :  function ( value ) {
    jika ( ! value) kembali  ' Bisa \' t Kosong ' ;
    kembali  benar ;
  }
},
{
  ketik : ' input ' ,
  nama : ' hastag ' ,
  pesan : ' [>] Sisipkan Hashtag (Tanpa #): ' ,
  validasi :  function ( value ) {
    jika ( ! value) kembali  ' Bisa \' t Kosong ' ;
    kembali  benar ;
  }
},
{
  ketik : ' input ' ,
  nama : ' mysyntx ' ,
  pesan : ' [>] Masukkan Total Target yang Anda Inginkan (ITTYW): ' ,
  validasi :  function ( value ) {
    nilai =  nilai . pertandingan ( / [ 0-9 ] / );
    jika (nilai) mengembalikan  true ;
    return  ' Gunakan Hanya Angka! ' ;
  }
},
{
  ketik : ' input ' ,
  nama : ' tidur ' ,
  pesan : ' [>] Sisipkan Tidur (Dalam MiliSeconds) ' ,
  validasi :  function ( value ) {
    nilai =  nilai . pertandingan ( / [ 0-9 ] / );
    jika (nilai) mengembalikan  true ;
    return  ' Delay is number ' ;
  }
}
]

const  doLogin  =  async ( params ) => {
  const  Device  =  new  Client.Device ( params . username );
   Penyimpanan  const =  Client.CookieMemoryStorage baru  ();
   sesi  const =  Client.Session baru  (Perangkat, Penyimpanan );
  coba {
    menunggu  Klien . Sesi . buat (Perangkat, Penyimpanan , params . nama pengguna , params . kata sandi )
     akun  const =  menunggu  sesi . getAccount ();
    mengembalikan  Janji . atasi ({sesi, akun});
  } catch (err) {
    mengembalikan  Janji . tolak (err);
  }
}

 fungsi  async ngeComment ( sesi , id , teks ) {
  coba {
    menunggu  Klien . Komentar . buat (sesi, id, teks);
    kembali  benar ;
  } catch (e) {
    return  false ;
  }
}

 fungsi  async ngeLike ( sesi , id ) {
  coba {
    menunggu  Klien . Seperti . buat (sesi, id)
    kembali  benar ;
  } catch (e) {
    return  false ;
  }
}

const  doAction  =  async ( sesi , params , teks ) => {
  if ( params . hasLiked ) {
           return chalk ` {bold.blue Sudah Disukai & Komentar} ` ;
  }
   tugas  const = [
  ngeLike (sesi, params . id ),
  ngeComment (sesi, params . id , teks)
  ];
  var [Suka, Komentar ] =  menunggu  Janji . semua (tugas);
  Komentar  =  Komentar  ? chalk ` {bold.green SUKSES} `  : chalk ` {bold.red GAGAL} ` ;
  Suka = Suka ? chalk ` {bold.green SUKSES} `  : chalk ` {bold.red GAGAL} ` ;
  return chalk ` [Like: $ { Like } ] [Komentar: $ { Comment } ({cyan $ { text } })] ` ;
}


const  doMain  =  async ( akun , hastag , sleep , mysyntx ) => {
  konsol . log (chalk ` {yellow \ n [?] Cobalah Masuk..} ` )
  akun =  menunggu  doLogin (akun);
  konsol . log (chalk ` {green [!] Sukses Masuk!} ` )
  const  pakan  =  baru  Client.Feed.TaggedMedia ( akun . sesi , hastag);
  konsol . log (chalk ` {cyan [?] Suka dan Komentari Semua Akun Di Hashtag: # $ { hastag } } ` );
  coba {
    kursor var ;
    jumlah var =  0 ;
    konsol . log (chalk ` {yellow \ n [#] [>] MULAI DENGAN RASIO $ { mysyntx } / $ { sleep } MiliSeconds [<] [#] \ n } ` )
    lakukan {
      jika (kursor) memberi makan . setCursor (kursor);
      hitung ++ ;  
      var media =  menunggu  umpan . dapatkan ();
      media =  _ . chunk (media, mysyntx);
      untuk (Media dari Media) {
        var timeNow =  Tanggal baru  ();
        timeNow =  ` $ { timeNow . getHours () } : $ { timeNow . getMinutes () } : $ { timeNow . getSeconds () } `
        menunggu  Janji . semua ( media . peta ( async (media) => {
	var  Text  =  fs . readFileSync ( ' komen.txt ' , ' utf8 ' ). split ( ' | ' );
          const  resultAction  =  menunggu  doAction ( akun . sesi , Media . params , Text );
          konsol . log (chalk ` [{magenta $ { timeNow } }] $ { media . id } | {cyanBright @ $ { media . params . akun . nama pengguna } } \ n => $ { resultAction } ` );
        }))
        konsol . log (chalk ` {yellow \ n [#] [>] Delay Untuk $ { sleep } MiliSeconds [<] [#] \ n } ` )
        menunggu  keterlambatan (tidur);
      }
      kursor =  menunggu  umpan . getCursor ();
      konsol . log (chalk ` [Kursor: {bold.cyan $ { kursor ? kursor :  ' null ' } } | Count: {bold.cyan $ { count } } | Total Media: {bold.cyan $ { media . panjang } } | Keterlambatan: $ { sleep } MiliSeconds] ` );
    } while ( feed . isMoreAvailable ());
  } catch (e) {
    konsol . log (e);
  }
}

konsol . log (chalk `
  {bold.cyan
  ————————————————— [INFORMASI] ——————————————————————
  [?] {bold.green Komentar & Suka | Menggunakan Target Hastag!}
  [?] {bold.green Gunakan komen.txt untk komen!}
  ------------------ [TERIMAKASIH UNTUK] --------------------
  [✓] KODE OLEH CYBER SCREAMER CCOCOT (ccocot@bc0de.net)
  [✓] MEMPERBAIKI & MENGUJI OLEH SYNTAX (@officialputu_id)
  [✓] CCOCOT.CO | BC0DE.NET | NAONLAH.NET | WingkoColi
  [✓] SGB TIM REBORN | Zerobyte.id | ccocot@bc0de.net
  ————————————————————————————————————————————————————— ———
  Apa yang baru?
  1. Input Target / delay Manual (ITTYW)
  ———————————————————————————————————————————————————— ———}
      ` );
// ikiganteng
penanya . prompt (pertanyaan)
. lalu ( jawaban  => {
  doMain ({
    nama pengguna : jawaban . nama pengguna ,
    kata sandi : jawaban . kata sandi }, jawaban . hastag , jawaban . tidur , jawaban . mysyntx );
})
. catch ( e  => {
  konsol . log (e);
})
