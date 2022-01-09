# Anoma-Node-Kurulum-Rehberi---Feigenbaum-
Feigenbaum Testneti için node kurulum yönergesi.
### 1- Gerekli Bağımlılıkları Yüklüyoruz
```
sudo apt update && sudo apt upgrade -y
sudo apt install make clang pkg-config libssl-dev libclang-dev build-essential git curl ntp jq llvm tmux
```
### 2- Rust Yüklüyoruz
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
```
### 3- Anoma Reposundan Kaynak Dosyalarını Çekelim
```
cd $HOME
git clone https://github.com/anoma/anoma.git
```
### 4- Versiyon Kontrolünü Yapalım
Anoma klasörüne geçiyoruz
```
cd $HOME/anoma
```
Anoma klasörünün için de şu kodu çalıştıralım : 
```
git checkout v0.2.0
```
### 5- Wasm Klasörünü $HOME Dizinine Kopyalıyoruz
```
cp -a $HOME/anoma/wasm $HOME/wasm
```
### 6- Anoma'yı Kaynaktan Yüklüyoruz
screen açalım
```
screen -S anoma
```
Anoma klasörüne geçelim
```
cd $HOME/anoma
```
Yükleme işlemini başlatıyoruz
```
make install
```
Yüklemenin tamamlanmasını bekleyelim

### 7- Düğümümüzü Feigenbaum test ağına katılacak şekilde yapılandırıyoruz
```
cd $HOME
anoma client utils join-network --chain-id=anoma-feigenbaum-0.ebb9e9f9013
```
### 8- Yeni bir cüzdan anahtarı oluşturalım
```
anoma wallet key gen --alias my-key
```
### 9- Anoma Düğümünü Başlatıyoruz
Ayrı bir screen penceresi açalım, bunun için screen içindeyken şu tuş kombinasyonunu kullanıyoruz :
**CTRL + A + C** 
Ardından :
```
anoma ledger
```
Node'un Senkronize olması için bekleyeceğiz.
### 10- Hesabımızı başlatıyoruz
```
anoma client init-account --alias my-new-acc --public-key my-key --source my-key
```
Başarılı yürütmeden sonra şöyle bir çıktı görmelisiniz:

`The transaction initialized 1 new account Added alias my-new-acc for address atest1kjdslahfaksjdhfajdhfkjadshfjkdfakjdhfakjlakdhfjhakdjhadkjfhaksdf`
### 11- Validator Oluşturma 
Faucet kullanarak biraz token istiyoruz
```
anoma client transfer --source faucet --target my-new-acc --signer my-new-acc --token XAN --amount 1000
```
Bakiyemizi kontrol edelim
```
anoma client balance --token XAN --owner my-new-acc
```
Böyle bir çıktı görmelisiniz: **XAN: 100**
### 12- Validatorümüzü Kaydediyoruz 
```
anoma client init-validator --alias my-validator --source my-new-acc
```
**my-validator** doğrulayıcınızın adıdır.

İşlem sırasında şifre girmeniz gerekecek, lütfen şifrenizi kaydedin.

Başarılı işlemden sonra aşağıdaki gibi bir çıktı olmalı : 
```
Added alias my-validator for address atest1vakldhfalksdjhflksajdhfkjashdfkljhsad.
Added alias my-validator-rewards for address atest1v4eaksdfkahdlkfjhasldkjhflakjs.
The validator's addresses and keys were stored in the wallet:
  Validator address "my-validator"
  Staking reward address "my-validator-rewards"
  Validator account key "my-validator-key"
  Consensus key "my-validator-consensus-key"
  Staking reward key "my-validator-rewards-key"
The ledger node has been setup to use this validator's address and consensus key.
```
### 13- Nodeumuzu Yeniden Başlatalım
Öncesinde çalışan `anoma ledger` işlemini yeniden başlatmamız gerekir
### 14- Tokenleri, Validatörümüze Bondluyoruz
```
anoma client bond --source my-new-acc --validator my-validator --amount 900.00
```
Delegasyon işlemini sorgulayabiliriz : 
```
anoma client bonds --owner my-new-acc
```
