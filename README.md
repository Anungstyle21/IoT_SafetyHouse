#include <Wire.h>
#include <RTClib.h> // Library RTC DS1307
#include <LiquidCrystal_I2C.h> // Library untuk LCD I2C
#include <DHT.h>

#define DHTPIN 4
#define DHTTYPE DHT22

// Konfigurasi pin SDA dan SCL untuk RTC DS1307
const int SDA_PIN = 21;
const int SCL_PIN = 22;

RTC_DS1307 rtc; // Objek RTC DS1307
LiquidCrystal_I2C lcd(0x27, 20, 4); // Objek LCD I2C
DHT dht(DHTPIN, DHTTYPE);

String message = "UPT BLK SURABAYA "; // Pesan yang akan ditampilkan dengan teks berjalan
String message1 = "Masuk Sini Aja ";
String message2 = "Cepat Kuyy ";

int messageLength; // Panjang pesan
int messageLength1;
int messageLength2;

void setup() {
  Serial.begin(9600);
  dht.begin();
  Wire.begin(SDA_PIN, SCL_PIN); // Mulai komunikasi I2C
  lcd.init(); // Inisialisasi LCD
  lcd.backlight(); // Nyalakan backlight LCD

  // Mulai RTC DS1307
  if (!rtc.begin()) {
    Serial.println("Tidak bisa menemukan RTC");
    while (1);
  }

  // Cek apakah RTC berjalan dengan benar
  if (!rtc.isrunning()) {
    Serial.println("RTC tidak berjalan. Setting waktu...");
    rtc.adjust(DateTime(F(__DATE__), F(__TIME__))); // Setting waktu RTC menggunakan waktu kompilasi
  }

  messageLength = message.length();
  messageLength1 = message1.length();
}

void loop() {

  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();

  if(isnan(humidity) || isnan(temperature)){
    Serial.println("Gagal membaca sensor DHT!");
  } else{
    Serial.print("Suhu:");
    Serial.print(temperature);
    Serial.print("°C, Kelembaban:");
    Serial.print(humidity);
    Serial.println("%");
  }
  DateTime now = rtc.now(); // Baca waktu dari RTC

  lcd.clear(); // Bersihkan layar LCD
  
  // Tampilkan tanggal dan waktu pada LCD
  lcd.setCursor(0, 0);
  lcd.print("Anung Style Bukhori");
  
  lcd.setCursor(0, 1);
  lcd.print("UPT BLK SURABAYA");

  lcd.setCursor(0, 2);
  lcd.print("Masuk Sini Aja");

  lcd.setCursor(0, 3);
  lcd.print("Cepat Kuyyy");

  Serial.print(now.year(), DEC);
  Serial.print('/');
  Serial.print(now.month(), DEC);
  Serial.print('/');
  Serial.println(now.day(), DEC);

  // Tampilkan pesan berjalan pada LCD
  for (int i = 0; i < messageLength + 16; i++) {
    lcd.setCursor(0, 1);
    lcd.print(message.substring(i, i + 16)); // Tampilkan potongan pesan sepanjang 16 karakter

    delay(200); // Tunggu 300 milidetik sebelum menggeser teks
    
  }
  for (int i = 0; i < messageLength1 + 20; i++) {
    lcd.setCursor(0, 2);
    lcd.print(message1.substring(i, i + 20)); // Tampilkan potongan pesan sepanjang 16 karakter

    delay(200); // Tunggu 300 milidetik sebelum menggeser teks
  }
  for (int i = 0; i < messageLength2 + 20; i++) {
    lcd.setCursor(0, 3);
    lcd.print(message2.substring(i, i + 20)); // Tampilkan potongan pesan sepanjang 16 karakter

    delay(300); // Tunggu 300 milidetik sebelum menggeser teks
  }
}
