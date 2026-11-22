# <h1 align="center">Laporan Praktikum Modul 8 - Queue</h1>
<p align="center">Muhammad Dhimas Hafizh Fathurrahman - 2311102151</p>

## Dasar Teori

Queue atau antrean adalah struktur data linear yang cara kerjanya mirip orang yang berbaris di loket, misalnya loket tiket kereta api. Aturan yang berlaku disebut **FIFO (First In First Out)**, artinya data yang lebih dulu masuk akan lebih dulu dilayani dan keluar dari antrean [1]. Struktur ini bisa dibangun dengan array, baik berbentuk linear maupun melingkar, atau dengan linked list [3]. Penerapannya banyak ditemui pada sistem pendaftaran pasien di klinik dan puskesmas [2][3], juga pada pengaturan prioritas layanan di rumah sakit [4].

### A. Konsep Dasar Queue<br/>
Sebuah queue dikelola lewat dua penanda, yaitu **HEAD** yang menunjuk elemen terdepan dan **TAIL** yang menunjuk elemen terbelakang. Data baru selalu dimasukkan lewat TAIL, sedangkan data yang dilayani selalu diambil dari HEAD [1]. Pada praktikum ini, queue yang belum berisi data ditandai dengan nilai `head = -1` dan `tail = -1`.
#### 1. Enqueue
Enqueue adalah proses memasukkan elemen baru di ujung belakang antrean. Pada queue berbasis array, ketersediaan ruang harus diperiksa lebih dulu agar data tidak melebihi kapasitas.
#### 2. Dequeue
Dequeue adalah proses mengeluarkan elemen yang berada di ujung depan antrean. Pada queue berbasis array, perlu dipastikan dahulu bahwa antrean tidak kosong.
#### 3. Operasi Pendukung
Selain dua operasi utama, terdapat `createQueue` untuk membentuk antrean kosong, `isEmptyQueue` untuk memeriksa apakah antrean kosong, `isFullQueue` untuk memeriksa apakah antrean penuh, dan `printInfo` untuk menampilkan isi antrean.

### B. Representasi Queue dengan Tabel (Array)<br/>
Queue berbasis tabel memiliki kapasitas yang terbatas dan tidak melibatkan pengelolaan memori dinamis, sedangkan queue berbasis pointer kapasitasnya tidak dibatasi [1]. Modul membedakan representasi tabel ke dalam tiga alternatif.
#### 1. Alternatif 1 (Head Diam, Tail Bergerak)
Selama antrean berisi, HEAD tetap berada di indeks pertama dan hanya TAIL yang maju ketika ada data masuk. Ketika data keluar, seluruh elemen sisa digeser ke depan dan TAIL mundur satu posisi. Mekanisme ini menyerupai orang yang melangkah maju saat antrean berkurang, namun kurang efisien karena harus melakukan penggeseran [1].
#### 2. Alternatif 2 (Head Bergerak, Tail Bergerak)
Pada alternatif ini HEAD ikut maju setiap kali ada data keluar, sehingga penggeseran pada dequeue tidak diperlukan. Masalahnya, antrean bisa terlihat penuh padahal masih ada ruang kosong di sisi depan HEAD (penuh semu). Untuk mengatasinya, elemen digeser ke awal tabel, dan hal ini hanya dilakukan saat TAIL telah menyentuh indeks maksimum [1].
#### 3. Alternatif 3 (Head dan Tail Berputar / Circular Queue)
Pada alternatif ini HEAD dan TAIL bergerak melingkar: setelah mencapai indeks terakhir, keduanya kembali ke indeks awal. Teknik ini dikenal sebagai *circular buffer* dan hemat karena tidak butuh penggeseran data [1]. Slot yang sudah ditinggalkan bisa langsung diisi data baru tanpa memindahkan elemen lain, sehingga memori dan waktu proses lebih terjaga [2].

## Guided 

### 1. Implementasi ADT Queue dengan Linked List (representasi pointer)

**queue.h**
```C++
/* File : queue.h
   Contoh ADT queue dengan representasi fisik pointer
   Representasi address dengan pointer
   Tipe info adalah integer */

#ifndef QUEUE_H_INCLUDE
#define QUEUE_H_INCLUDE

#include <iostream>
#define Nil NULL

typedef int infotype;
typedef struct elmQ *address;

struct elmQ {
    infotype info;
    address next;
};

/* Deklarasi tipe data queue, terdiri dari head dan tail, 
   queue kosong jika head = Nil */
struct queue {
    address head;
    address tail;
};

/* Prototype */

/********* Pengecekan apakah queue kosong ***********/
bool isEmpty(queue Q);
/* Mengembalikan nilai true jika queue kosong */

/********* Pembuatan queue kosong *********/
void CreateQueue(queue &Q);
/* I.S. sembarang
   F.S. terbentuk queue kosong */

/********* Manajemen memori *********/
address alokasi(infotype X);
/* Mengirimkan address dari alokasi elemen
   Jika alokasi berhasil, maka nilai address tidak Nil
   dan jika gagal, nilai address Nil */

void dealokasi(address P);
/* I.S. P terdefinisi
   F.S. memori yang digunakan P dikembalikan ke sistem */

/********* Pencarian sebuah elemen list *********/
address findElm(queue Q, infotype X);
/* Mencari apakah ada elemen queue dengan P->info = X
   Jika ada, mengembalikan address elemen tsb, dan Nil jika sebaliknya */

bool fFindElm(queue Q, address P);
/* Mencari apakah ada elemen queue dengan alamat P
   Mengembalikan true jika ada dan false jika tidak ada */

/********* Penambahan elemen **********/
void insert(queue &Q, address P);
/* I.S. sembarang, P sudah dialokasikan
   F.S. menempatkan elemen beralamat P pada akhir queue */

/********* Penghapusan sebuah elemen **********/
void del(queue &Q, address &P);
/* I.S. queue tidak kosong
   F.S. P menunjuk elemen pertama queue, head dari queue menunjuk pada 
        next elemen head yang lama. Queue mungkin menjadi kosong */

/********* Proses semua elemen queue **********/
void printInfo(queue Q);
/* I.S. queue mungkin tidak kosong
   F.S. jika queue tidak kosong, maka menampilkan semua info yang ada pada queue */

int nbList(queue Q);
/* Mengembalikan jumlah elemen pada queue */

void delAll(queue &Q);
/* Menghapus semua elemen pada queue dan semua elemen di-dealokasi */

#endif
```

**queue.cpp**
```C++
#include "queue.h"
using namespace std;

bool isEmpty(queue Q) {
    return (Q.head == Nil);
}

void CreateQueue(queue &Q) {
    Q.head = Nil;
    Q.tail = Nil;
}

address alokasi(infotype X) {
    address P = new (nothrow) elmQ;
    if (P != Nil) {
        P->info = X;
        P->next = Nil;
    }
    return P;
}

void dealokasi(address P) {
    delete P;
}

address findElm(queue Q, infotype X) {
    address P = Q.head;
    while (P != Nil) {
        if (P->info == X) {
            return P;
        }
        P = P->next;
    }
    return Nil;
}

bool fFindElm(queue Q, address P) {
    address R = Q.head;
    while (R != Nil) {
        if (R == P) {
            return true;
        }
        R = R->next;
    }
    return false;
}

void insert(queue &Q, address P) {
    if (P != Nil) {
        if (isEmpty(Q)) {
            Q.head = P;
            Q.tail = P;
        } else {
            (Q.tail)->next = P;
            Q.tail = P;
        }
    }
}

void del(queue &Q, address &P) {
    if (!isEmpty(Q)) {
        P = Q.head;
        if (Q.head == Q.tail) {
            Q.head = Nil;
            Q.tail = Nil;
        } else {
            Q.head = P->next;
        }
        P->next = Nil;
    } else {
        P = Nil;
    }
}

void printInfo(queue Q) {
    if (isEmpty(Q)) {
        cout << "Queue kosong" << endl;
    } else {
        address P = Q.head;
        while (P != Nil) {
            cout << P->info << " ";
            P = P->next;
        }
        cout << endl;
    }
}

int nbList(queue Q) {
    int jumlah = 0;
    address P = Q.head;
    while (P != Nil) {
        jumlah++;
        P = P->next;
    }
    return jumlah;
}

void delAll(queue &Q) {
    address P;
    while (!isEmpty(Q)) {
        del(Q, P);
        dealokasi(P);
    }
}
```

**main.cpp**
```C++
#include <iostream>
#include "queue.h"
using namespace std;

int main() {
    queue Q;
    address P;

    CreateQueue(Q);
    cout << "IsEmpty : " << isEmpty(Q) << endl;
    printInfo(Q);

    insert(Q, alokasi(5));
    insert(Q, alokasi(2));
    insert(Q, alokasi(7));
    cout << "Setelah insert 5, 2, 7" << endl;
    printInfo(Q);
    cout << "nbList : " << nbList(Q) << endl;

    P = findElm(Q, 2);
    if (P != Nil) {
        cout << "findElm(2) ketemu, info = " << P->info << endl;
        cout << "fFindElm : " << fFindElm(Q, P) << endl;
    }
    if (findElm(Q, 9) == Nil) {
        cout << "findElm(9) tidak ketemu" << endl;
    }

    del(Q, P);
    cout << "Setelah delete, Elemen yang keluar = " << P->info << endl;
    dealokasi(P);
    printInfo(Q);

    delAll(Q);
    cout << "Setelah delAll" << endl;
    printInfo(Q);
    cout << "IsEmpty : " << isEmpty(Q) << endl;

    return 0;
}
```
### Output Guided 1 :

```
IsEmpty : 1
Queue kosong
Setelah insert 5, 2, 7
5 2 7 
nbList : 3
findElm(2) ketemu, info = 2
fFindElm : 1
findElm(9) tidak ketemu
Setelah delete, Elemen yang keluar = 5
2 7 
Setelah delAll
Queue kosong
IsEmpty : 1
```

##### Output 1
![Screenshot Output Guided 1_1](https://github.com/(username github kalian)/(nama repository github kalian)/blob/main/Pertemuan8_Modul8/Output-Guided1-1.png)

Pada Guided 1, queue dibuat dengan **linked list** atau representasi pointer. Tiap elemen bertipe `elmQ` menyimpan data (`info`) dan alamat elemen sesudahnya (`next`). Struktur `queue` cukup memegang dua pointer: `head` yang menunjuk elemen paling depan dan `tail` yang menunjuk elemen paling belakang. Antrean dinyatakan kosong ketika `head` bernilai `Nil`, dan `CreateQueue` membentuk antrean kosong dengan mengisi `head` dan `tail` dengan `Nil`. Karena elemen dibuat satu per satu saat dibutuhkan, jumlah data tidak dibatasi seperti pada queue array di bagian unguided [1].

Pengelolaan memori dibagi dua. `alokasi` membuat elemen baru dengan `new (nothrow)`, menyimpan nilai ke `info`, lalu mengatur `next` ke `Nil`; bila pembuatan gagal, hasilnya `Nil`. `dealokasi` melepas memori sebuah elemen dengan `delete`. Untuk menambah data, `insert` (enqueue) menaruh elemen di ujung belakang: pada antrean kosong, `head` dan `tail` sama-sama diarahkan ke elemen tersebut, sedangkan pada antrean berisi, `next` milik `tail` diarahkan ke elemen baru dan `tail` dipindah ke elemen itu. Untuk mengeluarkan data, `del` (dequeue) mengambil elemen di `head` melalui `P`, lalu memajukan `head` ke elemen berikutnya; jika hanya ada satu elemen, `head` dan `tail` kembali menjadi `Nil`. Nama `del` dipilih karena `delete` sudah menjadi kata kunci bawaan C++.

Fungsi pelengkapnya: `findElm` mencari elemen berdasarkan isi `info`, `fFindElm` memeriksa apakah sebuah alamat benar-benar ada dalam antrean, `printInfo` mencetak isi antrean dari depan ke belakang, `nbList` menghitung banyaknya elemen, dan `delAll` mengosongkan antrean sambil mendealokasi semua elemennya.

Hasil eksekusi mengikuti urutan perintah di `main`. Antrean yang baru dibuat kosong (`IsEmpty : 1`). Sesudah memasukkan 5, 2, dan 7, isinya `5 2 7` dengan `nbList` sama dengan 3. Pencarian nilai 2 berhasil, sementara pencarian nilai 9 gagal karena tidak ada. Ketika `del` dijalankan, elemen yang keluar adalah **5**, data yang paling awal masuk, sehingga prinsip FIFO terpenuhi dan antrean tersisa `2 7`. Pada akhirnya `delAll` membersihkan seluruh elemen, dan `printInfo` menampilkan "Queue kosong".

### 2. Implementasi Queue dengan Array untuk Menyimpan Nama (tiga mekanisme dalam satu file)

**queue.h**
```C++
#ifndef QUEUE_H
#define QUEUE_H

#include <iostream>
using namespace std;

const int MAKSIMAL = 5;

struct queue {
    string nama[MAKSIMAL];
    int head;
    int tail;
};

bool isFull(queue Q);
bool isEmpty(queue Q);
void CreateQueue(queue &Q);
void enQueue(queue &Q, string nama);
void deQueue(queue &Q);
void viewQueue(queue Q);

#endif
```

**queue.cpp**
```C++
#include "queue.h"
#include <iostream>

using namespace std;

// NOTE : 
// Implementasi 1 = head diam, tail bergerak (Queue Linear Statis, kerana head nya tetap diam)
// Implementasi 2 = head bergerak, tail bergerak (Queue Linear Dinamis, karena head & tail nya sama-sama bergerak)
// Implementasi 3 = head dan tail berputar (Queue Circular, karena jika udh mentok tapi masih ada space, diputar sehingga tail bisa ada didepan head)

bool isEmpty(queue Q){
    if(Q.head == -1 && Q.tail == -1){
        return true;
    } else {
        return false;
    }
}

//isFull implmenetasi 1 & 2
bool isFull(queue Q){
    if(Q.tail == MAKSIMAL - 1){
        return true;
    } else {
        return false;
    }
}

// //isFull implementasi 3
// bool isFull(queue Q){
//     if((Q.tail + 1) % MAKSIMAL == Q.head){
//         return true;
//     } else {
//         return false;
//     }
// }

void CreateQueue(queue &Q){ //terbentuk queue dengan head = -1 dan tail = -1 
    Q.head = -1;
    Q.tail = -1;
}
 

//enqueue implementasi 1 & 2
void enQueue(queue &Q, string nama){
    if(isFull(Q) == true){
        cout << "Queue sudah penuh!" << endl;
    } else {
        if(isEmpty(Q) == true){
            Q.head = Q.tail = 0;
        } else {
            Q.tail++;
        }
        Q.nama[Q.tail] = nama;
        cout << "nama " << nama << " berhasil ditambahkan kedalam queue!" << endl;
    }
}

// //enQueue implementasi 3
// void enQueue(queue &Q, string nama){
//     if(isFull(Q) == true){
//         cout << "Queue sudah penuh!" << endl;
//     } else {
//         if(isEmpty(Q) == true){
//             Q.head = Q.tail = 0;
//         } else {
//             Q.tail = (Q.tail + 1) % MAKSIMAL; // bergerak melingkar
//         }
//         Q.nama[Q.tail] = nama;
//         cout << "nama " << nama << " berhasil ditambahkan kedalam queue!" << endl;
//     }
// }

//dequeue implementasi 1
void deQueue(queue &Q){
    if(isEmpty(Q) == true){
        cout << "Queue kosong!" << endl;
    } else {
        cout << "Mengahapus data " << Q.nama[Q.head] << "..." << endl;
        for(int i = 0; i < Q.tail; i++){
            Q.nama[i] =  Q.nama[i+1];
        }
        Q.tail--;
        if(Q.tail < 0){ //kalo semua isi queue nya udh dikelaurin, set head & tail ke -1
            Q.head = -1;
            Q.tail = -1;
        }
    }
}

// //dequeue implementasi 2
// void deQueue(queue &Q){
//     if(isEmpty(Q) == true){
//         cout << "Queue kosong!" << endl;
//     } else {
//         cout << "Mengahapus data " << Q.nama[Q.head] << "..." << endl;
//         Q.head++;
//         if(Q.head > Q.tail){ //kalo elemennya udh abis (head akan lebih 1 dari tail), maka reset ulang head & tail ke -1
//             Q.head = -1;
//             Q.tail = -1;
//         }
//     }
// }

// //deQueue implementasi 3
// void deQueue(queue &Q){
//     if(isEmpty(Q) == true){
//         cout << "Queue kosong!" << endl;
//     } else {
//         cout << "Mengahapus data " << Q.nama[Q.head] << "..." << endl;
//         if(Q.head == Q.tail){ //kalo elemennya tinggal 1, langsungkan saja head & tail nya reset ke -1
//             Q.head = -1;
//             Q.tail = -1;
//         } else {
//             Q.head = (Q.head + 1) % MAKSIMAL; // bergerak melingkar
//         }
//     }
// }

//viewQueue implementasi 1 & 2
void viewQueue(queue Q){
    if(isEmpty(Q) == true){
        cout << "Queue kosong!" << endl;
    } else {
        for(int i = Q.head; i <= Q.tail; i++){
            cout << i -  Q.head + 1 << ". " << Q.nama[i] << endl;
        }
    }
    cout << endl;
}

// //viewQueue implementasi 3
// void viewQueue(queue Q){
//     if(isEmpty(Q) == true){
//         cout << "Queue kosong!" << endl;
//     } else {
//         int i = Q.head;
//         int count = 1;
//         while(true){
//             cout << count << ". " << Q.nama[i] << endl;
//             if(i == Q.tail){
//                 break;
//             }
//             i = (i + 1) % MAKSIMAL;
//             count++;
//         }   
//     }
// }
```

**main.cpp**
```C++
#include "queue.h"
#include <iostream>

using namespace std;

int main(){
    queue Q;

    CreateQueue(Q);
    enQueue(Q, "Dimas");
    enQueue(Q, "Arvin");
    enQueue(Q, "Rizal");
    enQueue(Q, "Hafizh");
    enQueue(Q, "Fathur");
    enQueue(Q, "Daffa");
    cout << endl;

    cout << "--- Isi Queue Setelah enQueue ---" << endl;
    viewQueue(Q);
    cout << endl;

    deQueue(Q);
    deQueue(Q);
    deQueue(Q);
    deQueue(Q);
    // deQueue(Q);
    // deQueue(Q);
    cout << endl;

    cout << "--- Isi Queue Setelah deQueue ---" << endl;
    viewQueue(Q);

    return 0;
}
```
### Output Guided 2 :

```
nama Dimas berhasil ditambahkan kedalam queue!
nama Arvin berhasil ditambahkan kedalam queue!
nama Rizal berhasil ditambahkan kedalam queue!
nama Hafizh berhasil ditambahkan kedalam queue!
nama Fathur berhasil ditambahkan kedalam queue!
Queue sudah penuh!

--- Isi Queue Setelah enQueue ---
1. Dimas
2. Arvin
3. Rizal
4. Hafizh
5. Fathur


Mengahapus data Dimas...
Mengahapus data Arvin...
Mengahapus data Rizal...
Mengahapus data Hafizh...

--- Isi Queue Setelah deQueue ---
1. Fathur
```

##### Output 1
![Screenshot Output Guided 2_1](https://github.com/(username github kalian)/(nama repository github kalian)/blob/main/Pertemuan8_Modul8/Output-Guided2-1.png)

Guided 2 menyusun queue dari array `string` berkapasitas 5 (`MAKSIMAL = 5`) yang dipakai untuk menyimpan nama. Struktur `queue` memuat array `nama` beserta penanda `head` dan `tail`. Antrean yang belum terisi ditandai dengan `head = -1` dan `tail = -1` yang diatur lewat `CreateQueue`, lalu diperiksa oleh `isEmpty`. File `queue.cpp` memuat ketiga cara kerja queue array sekaligus, namun hanya satu yang dibiarkan aktif sementara dua lainnya dijadikan komentar. Yang berjalan pada program ini adalah **implementasi 1 (head diam, tail bergerak)**.

Dalam versi yang aktif, `isFull` memberi nilai benar saat `tail` telah menyentuh indeks terakhir (`MAKSIMAL - 1`). Jika antrean penuh, `enQueue` menampilkan "Queue sudah penuh!" dan tidak menyimpan apa pun. Jika masih muat, `head` dan `tail` diset ke 0 pada antrean kosong atau `tail` cukup ditambah satu pada antrean berisi, kemudian nama dimasukkan ke `nama[tail]`. `deQueue` lebih dulu mencetak nama di posisi `head` yang akan dibuang, kemudian menggeser semua elemen satu langkah ke depan dan menurunkan `tail`. Apabila `tail` menjadi di bawah 0, `head` dan `tail` dikembalikan ke -1. `viewQueue` menampilkan isi antrean dari `head` hingga `tail` lengkap dengan nomor urut.

Dua versi yang dikomentari adalah implementasi 2 dan 3. Pada implementasi 2 (head bergerak, tail bergerak), `deQueue` hanya menaikkan `head` tanpa menggeser data. Pada implementasi 3 (circular queue), pengecekan penuh memakai `(tail + 1) % MAKSIMAL == head`, dan `enQueue` serta `deQueue` memajukan `tail` dan `head` dengan modulo agar indeksnya melingkar. Penyajian ketiganya dalam satu file memudahkan perbandingan, dan sejalan dengan Unguided 1 sampai 3.

Pada eksekusi, enam nama dimasukkan ke antrean yang hanya muat lima. Lima nama pertama (Dimas, Arvin, Rizal, Hafizh, Fathur) tersimpan, sedangkan Daffa ditolak dengan pesan antrean penuh. Empat kali `deQueue` kemudian mengeluarkan Dimas, Arvin, Rizal, dan Hafizh sesuai urutan masuknya (FIFO), dan hanya Fathur yang tersisa di antrean.

### 3. ...

```C++
source code guided 3
```
penjelasan singkat guided 3

## Unguided 

### 1. Membuat ADT Queue dengan array menggunakan mekanisme Alternatif 1 (head diam, tail bergerak)

**queue.h**
```C++
#ifndef QUEUE_H
#define QUEUE_H

#include <iostream>
using namespace std;

typedef int infotype;
const int MAXQ = 5;

struct Queue {
    infotype info[MAXQ];
    int head, tail;
};

void createQueue(Queue &Q);
bool isEmptyQueue(Queue Q);
bool isFullQueue(Queue Q);
void enqueue(Queue &Q, infotype x);
infotype dequeue(Queue &Q);
void printInfo(Queue Q);

#endif
```

**queue.cpp**
```C++
#include "queue.h"

void createQueue(Queue &Q) {
    Q.head = -1;
    Q.tail = -1;
}

bool isEmptyQueue(Queue Q) {
    return (Q.head == -1 && Q.tail == -1);
}

bool isFullQueue(Queue Q) {
    return (Q.tail == MAXQ - 1);
}

void enqueue(Queue &Q, infotype x) {
    if (isFullQueue(Q)) {
        cout << "Queue penuh!" << endl;
    } else {
        if (isEmptyQueue(Q)) {
            Q.head = 0;
            Q.tail = 0;
        } else {
            Q.tail++;
        }
        Q.info[Q.tail] = x;
    }
}

infotype dequeue(Queue &Q) {
    if (isEmptyQueue(Q)) {
        cout << "Queue kosong!" << endl;
        return -1;
    }
    infotype x = Q.info[Q.head];
    // head diam, semua elemen digeser ke depan
    for (int i = Q.head; i < Q.tail; i++) {
        Q.info[i] = Q.info[i + 1];
    }
    Q.tail--;
    if (Q.tail < Q.head) {      // queue menjadi kosong
        Q.head = -1;
        Q.tail = -1;
    }
    return x;
}

void printInfo(Queue Q) {
    cout << Q.head << " - " << Q.tail << "\t | ";
    if (isEmptyQueue(Q)) {
        cout << "empty queue";
    } else {
        for (int i = Q.head; i <= Q.tail; i++) {
            cout << Q.info[i] << " ";
        }
    }
    cout << endl;
}
```

**main.cpp**
```C++
#include "queue.h"

int main() {
    cout << "Hello World" << endl;
    Queue Q;
    createQueue(Q);

    cout << "----------------------" << endl;
    cout << " H - T \t | Queue info" << endl;
    cout << "----------------------" << endl;
    printInfo(Q);
    enqueue(Q, 5); printInfo(Q);
    enqueue(Q, 2); printInfo(Q);
    enqueue(Q, 7); printInfo(Q);
    dequeue(Q);    printInfo(Q);
    enqueue(Q, 4); printInfo(Q);
    dequeue(Q);    printInfo(Q);
    dequeue(Q);    printInfo(Q);

    return 0;
}
```
### Output Unguided 1 :

```
Hello World
----------------------
 H - T 	 | Queue info
----------------------
-1 - -1	 | empty queue
0 - 0	 | 5 
0 - 1	 | 5 2 
0 - 2	 | 5 2 7 
0 - 1	 | 2 7 
0 - 2	 | 2 7 4 
0 - 1	 | 7 4 
0 - 0	 | 4 
```

##### Output 1
![Screenshot Output Unguided 1_1](https://github.com/(username github kalian)/(nama repository github kalian)/blob/main/Pertemuan8_Modul8/Output-Unguided1-1.png)

Program ini membangun ADT Queue berbasis array dengan kapasitas 5 elemen (`MAXQ = 5`) memakai Alternatif 1, yaitu **head tetap, tail bergerak**. Struktur `Queue` berisi array `info` beserta penanda `head` dan `tail`. `createQueue` menetapkan keduanya ke -1 untuk menandai antrean kosong, dan `isEmptyQueue` memeriksa kondisi itu. `isFullQueue` menghasilkan nilai benar ketika `tail` telah sampai di indeks terakhir (4).

Pada `enqueue`, jika antrean masih kosong maka `head` dan `tail` diset ke 0; jika tidak, `tail` cukup dinaikkan satu, lalu nilai baru disimpan di `info[tail]`. Pada `dequeue`, nilai di `info[head]` diambil terlebih dahulu, kemudian semua elemen sesudahnya digeser satu langkah ke depan dan `tail` dikurangi satu. Karena itu `head` selalu bernilai 0. Bila `tail` menjadi lebih kecil dari `head`, antrean dianggap kosong kembali dan keduanya diset ke -1.

Output mengikuti urutan pemanggilan di `main`. Setelah `enqueue` 5, 2, dan 7, isinya `5 2 7` (H-T = 0-2). `dequeue` mengeluarkan 5 sehingga tersisa `2 7` dan `tail` turun ke 1. Penambahan 4 membuat isinya `2 7 4`, lalu dua `dequeue` terakhir menyisakan `4` pada posisi 0-0. Pada seluruh proses, `head` tidak pernah beranjak dari 0.

### 2. Membuat implementasi ADT Queue dengan mekanisme Alternatif 2 (head bergerak, tail bergerak)

File `queue.h` sama dengan Unguided 1. Hanya `queue.cpp` yang berbeda.

**queue.cpp**
```C++
#include "queue.h"

void createQueue(Queue &Q) {
    Q.head = -1;
    Q.tail = -1;
}

bool isEmptyQueue(Queue Q) {
    return (Q.head == -1 && Q.tail == -1);
}

bool isFullQueue(Queue Q) {
    // penuh jika tail di ujung array DAN head sudah di posisi awal
    return (Q.head == 0 && Q.tail == MAXQ - 1);
}

void enqueue(Queue &Q, infotype x) {
    if (isFullQueue(Q)) {
        cout << "Queue penuh!" << endl;
        return;
    }
    if (isEmptyQueue(Q)) {
        Q.head = 0;
        Q.tail = 0;
    } else {
        if (Q.tail == MAXQ - 1) {
            // penuh "semu": geser elemen ke depan agar ada ruang kosong
            int n = Q.tail - Q.head + 1;
            for (int i = 0; i < n; i++) {
                Q.info[i] = Q.info[Q.head + i];
            }
            Q.head = 0;
            Q.tail = n - 1;
        }
        Q.tail++;
    }
    Q.info[Q.tail] = x;
}

infotype dequeue(Queue &Q) {
    if (isEmptyQueue(Q)) {
        cout << "Queue kosong!" << endl;
        return -1;
    }
    infotype x = Q.info[Q.head];
    if (Q.head == Q.tail) {     // elemen terakhir
        Q.head = -1;
        Q.tail = -1;
    } else {
        Q.head++;               // head bergerak maju
    }
    return x;
}

void printInfo(Queue Q) {
    cout << Q.head << " - " << Q.tail << "\t | ";
    if (isEmptyQueue(Q)) {
        cout << "empty queue";
    } else {
        for (int i = Q.head; i <= Q.tail; i++) {
            cout << Q.info[i] << " ";
        }
    }
    cout << endl;
}
```

**main.cpp**
```C++
#include "queue.h"

int main() {
    cout << "Hello World" << endl;
    Queue Q;
    createQueue(Q);

    cout << "----------------------" << endl;
    cout << " H - T \t | Queue info" << endl;
    cout << "----------------------" << endl;
    printInfo(Q);
    enqueue(Q, 5); printInfo(Q);
    enqueue(Q, 2); printInfo(Q);
    enqueue(Q, 7); printInfo(Q);
    dequeue(Q);    printInfo(Q);
    enqueue(Q, 4); printInfo(Q);
    dequeue(Q);    printInfo(Q);
    dequeue(Q);    printInfo(Q);

    // tambahan: menguji kondisi penuh "semu" dan pergeseran
    cout << "--- uji penuh semu ---" << endl;
    enqueue(Q, 8); printInfo(Q);
    enqueue(Q, 9); printInfo(Q);
    enqueue(Q, 6); printInfo(Q);   // tail di ujung, head > 0 -> geser
    enqueue(Q, 1); printInfo(Q);   // penuh sungguhan

    return 0;
}
```
### Output Unguided 2 :

```
Hello World
----------------------
 H - T 	 | Queue info
----------------------
-1 - -1	 | empty queue
0 - 0	 | 5 
0 - 1	 | 5 2 
0 - 2	 | 5 2 7 
1 - 2	 | 2 7 
1 - 3	 | 2 7 4 
2 - 3	 | 7 4 
3 - 3	 | 4 
--- uji penuh semu ---
3 - 4	 | 4 8 
0 - 2	 | 4 8 9 
0 - 3	 | 4 8 9 6 
0 - 4	 | 4 8 9 6 1 
```

##### Output 1
![Screenshot Output Unguided 2_1](https://github.com/(username github kalian)/(nama repository github kalian)/blob/main/Pertemuan8_Modul8/Output-Unguided2-1.png)

Pada Alternatif 2, **head ikut berpindah**. `dequeue` tidak lagi menggeser data; ia hanya menaikkan `head` satu langkah (`Q.head++`) sehingga prosesnya lebih ringan. Hal ini terlihat pada output: setelah `dequeue` pertama, H-T berubah dari 0-2 menjadi 1-2. Bila elemen yang diambil adalah satu-satunya yang tersisa (`head == tail`), antrean dikembalikan ke keadaan kosong (-1, -1).

Konsekuensinya adalah munculnya kondisi penuh semu [1]. Oleh sebab itu `isFullQueue` hanya bernilai benar jika `head == 0` **dan** `tail == MAXQ - 1`. Ketika `tail` sudah di ujung tetapi `head` masih lebih besar dari 0, `enqueue` lebih dulu memindahkan seluruh elemen ke awal array, baru kemudian menambahkan data baru. Penggeseran ini hanya terjadi saat `tail` menyentuh indeks maksimum, sesuai ketentuan modul.

Baris uji tambahan di `main` memperlihatkan mekanismenya. Setelah `enqueue(8)`, kondisinya `3 - 4` dengan `tail` di ujung. Pada `enqueue(9)`, elemen dipindahkan sehingga `head` kembali ke 0 dan `tail` menjadi 2 (isi `4 8 9`); tanpa pergeseran ini antrean akan keliru dianggap penuh. Sesudah `enqueue(6)` dan `enqueue(1)`, antrean terisi lima elemen (0-4) dan benar-benar penuh.

### 3. Membuat implementasi ADT Queue dengan mekanisme Alternatif 3 (head dan tail berputar)

File `queue.h` sama dengan Unguided 1. Hanya `queue.cpp` yang berbeda.

**queue.cpp**
```C++
#include "queue.h"

void createQueue(Queue &Q) {
    Q.head = -1;
    Q.tail = -1;
}

bool isEmptyQueue(Queue Q) {
    return (Q.head == -1 && Q.tail == -1);
}

bool isFullQueue(Queue Q) {
    // penuh jika successor dari tail adalah head
    return (!isEmptyQueue(Q) && (Q.tail + 1) % MAXQ == Q.head);
}

void enqueue(Queue &Q, infotype x) {
    if (isFullQueue(Q)) {
        cout << "Queue penuh!" << endl;
        return;
    }
    if (isEmptyQueue(Q)) {
        Q.head = 0;
        Q.tail = 0;
    } else {
        Q.tail = (Q.tail + 1) % MAXQ;   // tail berputar
    }
    Q.info[Q.tail] = x;
}

infotype dequeue(Queue &Q) {
    if (isEmptyQueue(Q)) {
        cout << "Queue kosong!" << endl;
        return -1;
    }
    infotype x = Q.info[Q.head];
    if (Q.head == Q.tail) {
        Q.head = -1;
        Q.tail = -1;
    } else {
        Q.head = (Q.head + 1) % MAXQ;   // head berputar
    }
    return x;
}

void printInfo(Queue Q) {
    cout << Q.head << " - " << Q.tail << "\t | ";
    if (isEmptyQueue(Q)) {
        cout << "empty queue";
    } else {
        int i = Q.head;
        while (true) {
            cout << Q.info[i] << " ";
            if (i == Q.tail) break;
            i = (i + 1) % MAXQ;
        }
    }
    cout << endl;
}
```

**main.cpp**
```C++
#include "queue.h"

int main() {
    cout << "Hello World" << endl;
    Queue Q;
    createQueue(Q);

    cout << "----------------------" << endl;
    cout << " H - T \t | Queue info" << endl;
    cout << "----------------------" << endl;
    printInfo(Q);
    enqueue(Q, 5); printInfo(Q);
    enqueue(Q, 2); printInfo(Q);
    enqueue(Q, 7); printInfo(Q);
    dequeue(Q);    printInfo(Q);
    enqueue(Q, 4); printInfo(Q);
    dequeue(Q);    printInfo(Q);
    dequeue(Q);    printInfo(Q);

    // tambahan: menguji perputaran (circular)
    cout << "--- uji circular ---" << endl;
    enqueue(Q, 8); printInfo(Q);
    enqueue(Q, 9); printInfo(Q);
    enqueue(Q, 6); printInfo(Q);   // tail berputar ke indeks 0
    enqueue(Q, 1); printInfo(Q);   // penuh
    enqueue(Q, 3); printInfo(Q);   // ditolak

    return 0;
}
```
### Output Unguided 3 :

```
Hello World
----------------------
 H - T 	 | Queue info
----------------------
-1 - -1	 | empty queue
0 - 0	 | 5 
0 - 1	 | 5 2 
0 - 2	 | 5 2 7 
1 - 2	 | 2 7 
1 - 3	 | 2 7 4 
2 - 3	 | 7 4 
3 - 3	 | 4 
--- uji circular ---
3 - 4	 | 4 8 
3 - 0	 | 4 8 9 
3 - 1	 | 4 8 9 6 
3 - 2	 | 4 8 9 6 1 
Queue penuh!
3 - 2	 | 4 8 9 6 1 
```

##### Output 1
![Screenshot Output Unguided 3_1](https://github.com/(username github kalian)/(nama repository github kalian)/blob/main/Pertemuan8_Modul8/Output-Unguided3-1.png)

Alternatif 3 menerapkan **circular queue**, yaitu `head` dan `tail` bergerak melingkar. Setiap kenaikan indeks dihitung dengan `(indeks + 1) % MAXQ`, sehingga setelah indeks 4 posisi kembali ke 0. Dengan demikian tidak ada elemen yang perlu digeser, baik pada `enqueue` maupun `dequeue`, dan masalah penuh semu pada Alternatif 2 tidak muncul [1][2].

Karena `head` dan `tail` dapat berada di posisi mana pun, kondisi penuh tidak lagi diperiksa dengan `tail == MAXQ - 1`, tetapi dengan `(tail + 1) % MAXQ == head`, yaitu ketika posisi setelah `tail` adalah `head`. `printInfo` pun menelusuri data mulai dari `head` secara melingkar hingga mencapai `tail`.

Pengujian tambahan di `main` memperlihatkan perputaran tersebut. Setelah `enqueue(8)` kondisinya `3 - 4`. Pada `enqueue(9)`, `tail` melompat ke indeks 0 sehingga H-T menjadi `3 - 0` dengan isi `4 8 9`, tanpa ada data yang dipindahkan. Sesudah `enqueue(6)` dan `enqueue(1)` antrean penuh (`3 - 2`), dan `enqueue(3)` ditolak dengan pesan "Queue penuh!". Hasil ini sejalan dengan temuan bahwa circular queue memakai kembali slot kosong tanpa menggeser data sehingga lebih efisien daripada linear queue [2].

## Kesimpulan
Queue adalah struktur data berprinsip FIFO, dengan data masuk lewat TAIL dan keluar lewat HEAD. Praktikum ini mengimplementasikannya pada array dengan tiga cara. Alternatif 1 (head tetap) paling mudah dipahami, tetapi boros karena tiap dequeue menggeser semua elemen. Alternatif 2 (head bergerak) membuat dequeue lebih ringan, namun rentan terhadap kondisi penuh semu sehingga perlu pergeseran ketika tail mencapai batas. Alternatif 3 (circular queue) paling efisien karena indeks berputar, tidak ada pergeseran, dan seluruh slot array dapat dipakai. Ketiganya sama-sama mengeluarkan data sesuai urutan masuk; perbedaannya terletak pada cara mengelola indeks dan tingkat efisiensinya.

## Referensi
[1] Modul Praktikum Struktur Data. (n.d.). Modul 8 Queue. Fakultas Informatika, Telkom University.
<br>[2] Aini, S. N., & Kurniawan, R. (2025). "Sistem Antrian Pasien Berbasis Web Dengan Algoritma Circular Queue Pada Klinik Pratama Rawat Inap Ar-Razi". CESS (Journal of Computer Engineering, System and Science), 10(2), 666–678. Diakses melalui https://jurnal.unimed.ac.id/2012/index.php/cess/article/download/67447/27654/155420.
<br>[3] Gunawan, R., Yuana, H., & Kirom, S. (2023). "Implementasi Metode Queue Pada Sistem Antrian Online Berbasis Web Studi Kasus UPTD Puskesmas Sananwetan". JATI (Jurnal Mahasiswa Teknik Informatika), 7(3). https://doi.org/10.36040/jati.v7i3.6687.
<br>[4] Aryandi, J. A., Nugraha, M. A., Basith, Y. A. A., Pratama, M. F., Pradeka, D., & Anggraini, D. (2023). "Implementasi Algoritma Queue untuk Menentukan Prioritas Pelayanan Umum di Rumah Sakit". JIKO (Jurnal Informatika dan Komputer), 7(2). https://doi.org/10.26798/jiko.v7i2.806.
