# Modul 6 – Interrupt dan Timer
Nama       : Nursyafika  
NIM        : H1H024023  

## Percobaan 6A – Interrupt Tombol LED
### Kode Program 6A

```cpp
#include <Arduino.h>

volatile bool ledState = false;

void tombolInterrupt() {
  ledState = !ledState;
}

void setup() {
  pinMode(13, OUTPUT);
  pinMode(2, INPUT_PULLUP);
  attachInterrupt(digitalPinToInterrupt(2), tombolInterrupt, FALLING);
}

void loop() {
  digitalWrite(13, ledState);
}

Penjelasan Kode 6A

Baris / Bagian	Penjelasan

#include <Arduino.h>	Menyertakan library dasar Arduino.
volatile bool ledState = false	Menyimpan kondisi LED dan dapat berubah dari interrupt.
void tombolInterrupt()	Fungsi ISR yang dijalankan saat tombol ditekan.
ledState = !ledState	Membalik kondisi LED.
pinMode(13, OUTPUT)	Mengatur pin 13 sebagai output LED.
pinMode(2, INPUT_PULLUP)	Mengatur pin 2 sebagai input tombol.
attachInterrupt(...)	Mengaktifkan interrupt pada pin 2 dengan mode FALLING.
digitalWrite(13, ledState)	Mengatur LED sesuai nilai ledState.


Pertanyaan Praktikum 6A

Pertanyaan 1: Jelaskan proses bagaimana tombol dapat mengubah kondisi LED menggunakan interrupt!

Ketika tombol ditekan, sinyal pada pin 2 berubah dari HIGH menjadi LOW karena menggunakan INPUT_PULLUP. Perubahan ini terdeteksi sebagai interrupt mode FALLING, lalu Arduino menjalankan fungsi tombolInterrupt(). Di dalam fungsi tersebut, nilai ledState dibalik, sehingga kondisi LED ikut berubah.

Pertanyaan 2: Apa fungsi attachInterrupt() pada program tersebut?

attachInterrupt() berfungsi untuk menghubungkan pin interrupt dengan fungsi ISR. Pada program ini, pin 2 akan memanggil fungsi tombolInterrupt() saat terjadi perubahan sinyal dari HIGH ke LOW.

Pertanyaan 3: Mengapa pada ISR tidak disarankan menggunakan delay() dan Serial.print()?

Karena ISR harus berjalan cepat. delay() dapat mengganggu kerja timer, sedangkan Serial.print() membutuhkan waktu lebih lama. Jika digunakan di ISR, program bisa menjadi lambat atau tidak stabil.

Pertanyaan 4: Apa fungsi keyword volatile pada variabel ledState?

volatile digunakan agar perubahan nilai ledState yang terjadi di dalam ISR tetap terbaca oleh program utama di loop().

Pertanyaan 5: Modifikasi menggunakan mode interrupt lain

Mode RISING

#include <Arduino.h>

volatile bool ledState = false;

void tombolInterrupt() {
  ledState = !ledState;
}

void setup() {
  pinMode(13, OUTPUT);
  pinMode(2, INPUT_PULLUP);
  attachInterrupt(digitalPinToInterrupt(2), tombolInterrupt, RISING);
}

void loop() {
  digitalWrite(13, ledState);
}

Mode RISING membuat LED berubah saat tombol dilepaskan.

Mode CHANGE

#include <Arduino.h>

volatile bool ledState = false;

void tombolInterrupt() {
  ledState = !ledState;
}

void setup() {
  pinMode(13, OUTPUT);
  pinMode(2, INPUT_PULLUP);
  attachInterrupt(digitalPinToInterrupt(2), tombolInterrupt, CHANGE);
}

void loop() {
  digitalWrite(13, ledState);
}

Mode CHANGE membuat LED berubah saat tombol ditekan dan dilepaskan.

Mode LOW

#include <Arduino.h>

volatile bool ledState = false;

void tombolInterrupt() {
  ledState = !ledState;
}

void setup() {
  pinMode(13, OUTPUT);
  pinMode(2, INPUT_PULLUP);
  attachInterrupt(digitalPinToInterrupt(2), tombolInterrupt, LOW);
}

void loop() {
  digitalWrite(13, ledState);
}

Mode LOW membuat interrupt aktif terus selama tombol ditekan, sehingga LED bisa berubah sangat cepat.


Percobaan 6B – Timer Non-Blocking dengan millis()

Kode Program 6B

#include <Arduino.h> 

unsigned long previousMillis = 0;
const long interval = 1000;
bool ledState = false;

void setup() { 
    pinMode(13, OUTPUT);
} 

void loop() { 
    unsigned long currentMillis = millis();

    if(currentMillis - previousMillis >= interval) { 
        previousMillis = currentMillis;
        ledState = !ledState;
        digitalWrite(13, ledState);
    } 
}

Penjelasan Kode 6B

Baris / Bagian	Penjelasan

#include <Arduino.h>	Menyertakan library dasar Arduino.
unsigned long previousMillis = 0	Menyimpan waktu terakhir LED berubah.
const long interval = 1000	Menentukan interval kedip LED 1000 ms.
bool ledState = false	Menyimpan kondisi LED saat ini.
pinMode(13, OUTPUT)	Mengatur pin 13 sebagai output LED.
unsigned long currentMillis = millis()	Mengambil waktu saat ini sejak Arduino dinyalakan.
currentMillis - previousMillis >= interval	Mengecek apakah sudah lewat 1000 ms.
previousMillis = currentMillis	Memperbarui waktu terakhir LED berubah.
ledState = !ledState	Membalik kondisi LED.
digitalWrite(13, ledState)	Menyalakan atau mematikan LED sesuai kondisi terbaru.


Pertanyaan Praktikum 6B

Pertanyaan 1: Jelaskan bagaimana fungsi millis() bekerja pada program tersebut!

millis() menghitung waktu dalam milidetik sejak Arduino dinyalakan. Pada program ini, nilai millis() dibandingkan dengan previousMillis. Jika selisihnya sudah mencapai 1000 ms, maka LED berubah kondisi.

Pertanyaan 2: Apa perbedaan utama antara delay() dan millis()?

delay() menghentikan program selama waktu tertentu, sedangkan millis() tidak menghentikan program. Dengan millis(), program tetap berjalan sambil menunggu waktu yang ditentukan tercapai.

Pertanyaan 3: Mengapa metode millis() disebut non-blocking?

Karena millis() tidak membuat program berhenti. Program tetap bisa menjalankan perintah lain di dalam loop() sambil mengecek waktu.

Pertanyaan 4: Modifikasi program agar dua LED berkedip tanpa delay()

#include <Arduino.h>

const int PIN_LED1 = 13;
const int PIN_LED2 = 12;

unsigned long prevMillis1 = 0;
const long interval1 = 1000;
bool ledState1 = false;

unsigned long prevMillis2 = 0;
const long interval2 = 500;
bool ledState2 = false;

void setup() {
  pinMode(PIN_LED1, OUTPUT);
  pinMode(PIN_LED2, OUTPUT);
}

void loop() {
  unsigned long now = millis();

  if (now - prevMillis1 >= interval1) {
    prevMillis1 = now;
    ledState1 = !ledState1;
    digitalWrite(PIN_LED1, ledState1);
  }

  if (now - prevMillis2 >= interval2) {
    prevMillis2 = now;
    ledState2 = !ledState2;
    digitalWrite(PIN_LED2, ledState2);
  }
}

LED pertama berkedip setiap 1 detik, sedangkan LED kedua berkedip setiap 500 ms. Karena tidak menggunakan delay(), kedua LED bisa bekerja seolah-olah bersamaan.


Pertanyaan Analisis

Pertanyaan 1: Sebutkan dan jelaskan keuntungan menggunakan interrupt dibanding polling!

Interrupt lebih efisien karena Arduino tidak perlu terus-menerus mengecek tombol. Arduino hanya merespons saat ada kejadian tertentu, sehingga program menjadi lebih cepat dan penggunaan CPU lebih hemat.

Pertanyaan 2: Mengapa timer penting dalam sistem embedded dan real-time?

Timer penting untuk mengatur pekerjaan berdasarkan waktu, seperti membuat LED berkedip, membaca sensor secara berkala, membuat PWM, dan mengatur timeout.

Pertanyaan 3: Jika interrupt dan timer digabung dalam satu sistem, bagaimana alur kerja sistem tersebut?

Program utama tetap berjalan di loop(). Timer mengatur tugas berbasis waktu, sedangkan interrupt menangani kejadian mendadak seperti tombol ditekan. Setelah ISR selesai, program kembali berjalan seperti semula.

Pertanyaan 4: Apa yang terjadi jika ISR terlalu panjang atau kompleks?

Jika ISR terlalu panjang, program utama akan tertunda dan sistem bisa menjadi lambat atau tidak stabil. Interrupt lain juga bisa terlambat atau terlewat. Karena itu, ISR sebaiknya dibuat singkat.