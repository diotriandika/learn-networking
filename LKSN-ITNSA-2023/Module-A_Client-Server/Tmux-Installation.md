# Langkah - Langkah Instalasi dan Konfigurasi Tmux pada Linux
Apa itu Tmux? Tmux atau _Terminal Multiplexing_ adalah sebuah aplikasi pada Linux yang dapat digunakan untuk multitasking di dalam terminal window yang disebut dengan Sessions. Pengguna bisa melakukan Process, pindah ke process lain, keluar dari process yang berjalan, dan masuk kembali ke process berjalan.

## Instalasi Tmux
Update package dan install Tmux
```
sudo apt install tmux
```
## Menggunakan Tmux
Di dalam Tmux, kita akan bekerja dengan **Sessions** **Windows** dan **Panes** jika diibaratkan seperti sebuah pohon, yang mana **Sessions** adalah pokok batang pohon, **Windows** adalah cabang dari pokok batang pohon, dan **Panes** adalah daun dari cabang pohon tersebut.
- **Sessions** mengdefinisikan task utama, jika memiliki keterangan (attached) disamping nama sessions, itu berarti session tersebut sedang terbuka
- **Windows** adalah untuk aktifitas yang spesifik dari sebuah session, atau anggap saja tab
- **Panes** akan membantu untuk membuat multiple views dari satu Window.

Jadi secara garis besar dapat diurutkan seperti ini.
```
server > sessions > windows > panes
```
