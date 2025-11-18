# flutter-firebase-integration
This repository contains up-to-date connection steps for flutter projects.

# Flutter Uygulamalarının Güncel Firebase’e Bağlama İşlemleri

Modern Flutter projelerinde Firebase entegrasyonu için **FlutterFire CLI** kullanımı standart haline gelmiştir. Bu yöntem, manuel dosya yapılandırmalarının (json/plist) yarattığı karmaşayı ortadan kaldırır.

## 1. FlutterFire CLI Nedir ve Neden Gereklidir?

Bu araç, komut satırı arayüzü ile yükleme yapmamızı sağlar ve şu işlemleri otomatikleştirir:

* Firebase projenize bağlanır.
* Projenizdeki Android ve iOS platformlarını otomatik olarak algılar.
* Gerekli platforma özgü Firebase uygulamalarını (eğer yoksa konsolda) oluşturur.
* Gerekli tüm yapılandırma dosyalarını ve Gradle eklentilerini otomatik olarak düzenler.
* Tüm platform anahtarlarını ve yapılandırmaları içeren, `lib/firebase_options.dart` adında tek bir Dart dosyası oluşturur.

---

## 2. Gerekli Kurulumlar (Node.js & NVM)

FlutterFire CLI, Google'ın ana Firebase komut satırı aracı olan `firebase-tools` paketine ihtiyaç duyar. Bu araç Node.js tabanlıdır. İzin sorunlarını (sudo kullanımı) önlemek ve sürüm yönetimi için **NVM** kullanılması önerilir.

### Node.js ve NVM Kurulumu (macOS/Linux)

NVM, Node.js sürümlerini ve global paketleri sistem dizinleri yerine sizin kendi kullanıcı (home) dizininiz (`~/.nvm`) altına kurar. Bu sayede `sudo` kullanma ihtiyacı ortadan kalkar.

1.  **NVM Kurulumu:**
    ```bash
    brew install nvm
    ```

2.  **Shell Yapılandırması:**
    `.zshrc` dosyanızı açın (`nano ~/.zshrc`) ve şu satırları en alta ekleyin:
    ```bash
    export NVM_DIR="$HOME/.nvm"
    [ -s "/opt/homebrew/opt/nvm/nvm.sh" ] && \. "/opt/homebrew/opt/nvm/nvm.sh"
    [ -s "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm" ] && \. "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm"
    ```

3.  **Aktifleştirme ve Node Yükleme:**
    Terminali yeniden başlatın veya `source ~/.zshrc` komutunu çalıştırın.
    ```bash
    nvm install 22  # (Sürüm değişebilir, güncel LTS sürümü önerilir)
    nvm use 22
    nvm alias default 22
    ```

---

## 3. Firebase CLI ve FlutterFire CLI Kurulumu

Terminal veya komut istemcisini açın ve aşağıdaki komutları sırasıyla çalıştırın:

1.  **Firebase Tools Kurulumu:**
    ```bash
    npm install -g firebase-tools
    ```

2.  **Giriş Yapma:**
    ```bash
    firebase login
    ```

3.  **FlutterFire CLI Aktivasyonu:**
    ```bash
    dart pub global activate flutterfire_cli
    ```

> **⚠️ DİKKAT (PATH Sorun Giderme):**
> Özellikle Windows kullanıcıları, `dart pub global` komutundan sonra bir uyarı (Warning) mesajı görebilir. Bu, çalıştırılabilir dosyaların `PATH` listenizde olmadığını belirtir.
>
> * **Hata:** `flutterfire` komutu "tanınmadı" hatası verir.
> * **Çözüm:** Uyarı mesajında belirtilen dizin yolunu (Örn: `C:\Users\[kullanıcı]\AppData\Local\Pub\Cache\bin`) sisteminizin **Ortam Değişkenleri (PATH)** listesine manuel olarak ekleyin ve terminali yeniden başlatın.

---

## 4. Mevcut Projeler İçin Geçiş (Migration) Adımları

**Kritik Eylem:** Eğer projenizde zaten manuel olarak eklenmiş `google-services.json` veya `GoogleService-Info.plist` dosyaları varsa, `flutterfire configure` komutunu çalıştırmadan önce temizlik yapmalısınız. Aksi takdirde **çift yapılandırma** sorunları oluşur.

**Temizlenecek Dosyalar ve Kodlar:**

1.  **Dosyaları Silin:**
    * `android/app/google-services.json`
    * `ios/Runner/GoogleService-Info.plist` (Xcode projesinden ve dosya sisteminden kaldırın).

2.  **Gradle Temizliği:**
    * `android/build.gradle` dosyasını açın ve `buildscript { dependencies {... } }` altındaki manuel `classpath 'com.google.gms:google-services:...'` satırını silin.
    * `android/app/build.gradle` dosyasını açın ve en üstteki `apply plugin: 'com.google.gms:google-services'` satırını silin.
    * `settings.gradle` içerisindeki google services eklemelerini kaldırın.

Bu temizlikten sonra `flutterfire configure` çalıştırmak, gerekli yapılandırmaları **doğru ve modern** yolla yeniden ekleyecektir.

---

## 5. Projede Başlatma

İlk yapılandırma komutunu proje dizininde çalıştırın:

* flutterfire configure

```bash
import 'package:firebase_core/firebase_core.dart';
import 'package:flutter/material.dart';
import 'firebase_options.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized(); 

  await Firebase.initializeApp(
    options: DefaultFirebaseOptions.currentPlatform,
  );

  runApp(const MyApp());
}
