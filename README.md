# revisi-algo-7
#include <iostream>
#include <string>
using namespace std;

// Konstanta Maksimal Menu dan Pesanan
const int MAX_MENU = 100;
const int MAX_PESANAN = 100;
const int MAX_ITEMS_PER_PESANAN = 10; // Maksimal item per pesanan

// Data Menu
string namaMenu[MAX_MENU];
double hargaMenu[MAX_MENU];
int ketersediaanMenu[MAX_MENU]; // 1 = tersedia, 0 = habis
int jumlahMenu = 0;

// Data Pesanan
string namaItemPesanan[MAX_PESANAN][MAX_ITEMS_PER_PESANAN];
int jumlahItemPesanan[MAX_PESANAN][MAX_ITEMS_PER_PESANAN];
double hargaItemPesanan[MAX_PESANAN][MAX_ITEMS_PER_PESANAN];
int jumlahJenisItemPesanan[MAX_PESANAN];
double totalHargaPesanan[MAX_PESANAN];

int jumlahPesananTercatat = 0;

// Fungsi untuk validasi input angka
template <typename T>
T validasiInput(T& input) {
    while (!(cin >> input)) {
        cin.clear();
        cin.ignore(1000, '\n');
        cout << "Input tidak valid, silakan coba lagi: ";
    }
    return input;
}

// Fungsi Menampilkan Daftar Menu
void tampilkanMenu() {
    if (jumlahMenu == 0) {
        cout << "\nDaftar menu kosong.\n";
        return;
    }

    cout << "\n=== Daftar Menu Warteg ===\n";
    cout << "No.  Nama Lauk            Harga     Status\n";
    cout << "=========================================\n";
    for (int i = 0; i < jumlahMenu; i++) {
        cout << i + 1 << ".   ";
        cout.width(20); cout << left << namaMenu[i];
        cout << "Rp " << hargaMenu[i] << "   ";
        cout << (ketersediaanMenu[i] ? "Tersedia" : "Habis") << endl;
    }
}

// Fungsi Input Menu
void inputMenu() {
    int jumlah;
    cout << "Masukkan jumlah menu yang ingin ditambahkan: ";
    validasiInput(jumlah);

    for (int i = 0; i < jumlah; i++) {
        cin.ignore();
        cout << "Masukkan nama menu ke-" << i + 1 << ": ";
        getline(cin, namaMenu[jumlahMenu]);
        cout << "Masukkan harga menu: ";
        validasiInput(hargaMenu[jumlahMenu]);
        ketersediaanMenu[jumlahMenu] = 1;
        jumlahMenu++;
    }
    cout << "Menu berhasil ditambahkan.\n";
}

// Fungsi Update Ketersediaan
void updateKetersediaan() {
    int nomorMenu;
    tampilkanMenu();
    cout << "Masukkan nomor menu yang akan diperbarui: ";
    validasiInput(nomorMenu);

    if (nomorMenu < 1 || nomorMenu > jumlahMenu) {
        cout << "Menu tidak ditemukan.\n";
        return;
    }

    int status;
    cout << "Masukkan status ketersediaan (1 = tersedia, 0 = habis): ";
    validasiInput(status);

    if (status != 0 && status != 1) {
        cout << "Input status tidak valid.\n";
        return;
    }

    ketersediaanMenu[nomorMenu - 1] = status;
    cout << "Status menu berhasil diperbarui.\n";
}

// Fungsi Input Pesanan
void inputPesanan() {
    char lanjut;
    int currentPesanan = jumlahPesananTercatat;
    jumlahJenisItemPesanan[currentPesanan] = 0;
    totalHargaPesanan[currentPesanan] = 0;

    do {
        tampilkanMenu();
        int nomorMenu, jumlah;

        cout << "\nMasukkan nomor lauk yang dipilih: ";
        validasiInput(nomorMenu);

        if (nomorMenu < 1 || nomorMenu > jumlahMenu) {
            cout << "Menu tidak ditemukan.\n";
            continue;
        }

        if (!ketersediaanMenu[nomorMenu - 1]) {
            cout << "Maaf, lauk yang dipilih habis.\n";
            continue;
        }

        cout << "Masukkan jumlah porsi: ";
        validasiInput(jumlah);

        if (jumlah <= 0) {
            cout << "Jumlah pesanan tidak valid.\n";
            continue;
        }

        int idx = jumlahJenisItemPesanan[currentPesanan];
        namaItemPesanan[currentPesanan][idx] = namaMenu[nomorMenu - 1];
        jumlahItemPesanan[currentPesanan][idx] = jumlah;
        hargaItemPesanan[currentPesanan][idx] = hargaMenu[nomorMenu - 1] * jumlah;
        totalHargaPesanan[currentPesanan] += hargaItemPesanan[currentPesanan][idx];
        jumlahJenisItemPesanan[currentPesanan]++;

        cout << "\nIngin menambah lauk lain? (y/n): ";
        cin >> lanjut;

    } while (lanjut == 'y' || lanjut == 'Y');

    jumlahPesananTercatat++;
    cout << "\nPesanan berhasil dicatat!\n";
}

// Fungsi Hitung Total Tagihan
void hitungTotalTagihan() {
    if (jumlahPesananTercatat == 0) {
        cout << "\nTidak ada pesanan untuk ditampilkan.\n";
        return;
    }

    cout << "\n=== Daftar Pesanan ===\n";
    for (int i = 0; i < jumlahPesananTercatat; i++) {
        cout << "\nPesanan #" << i + 1 << "\n";
        cout << "------------------------\n";
        for (int j = 0; j < jumlahJenisItemPesanan[i]; j++) {
            cout << j + 1 << ". " << namaItemPesanan[i][j]
                 << " x" << jumlahItemPesanan[i][j]
                 << " = Rp" << hargaItemPesanan[i][j] << endl;
        }
        cout << "------------------------\n";
        cout << "Total: Rp" << totalHargaPesanan[i] << endl;
    }
}

// Menu Utama
void menuUtama() {
    int pilihan;

    do {
        cout << "\n===== Warteg POS System =====\n";
        cout << "1. Input Menu (Owner)\n";
        cout << "2. Update Ketersediaan Menu (Staff)\n";
        cout << "3. Input Pesanan (Staff)\n";
        cout << "4. Hitung Total Tagihan (Staff)\n";
        cout << "5. Tampilkan Menu\n";
        cout << "6. Keluar\n";

        cout << "Pilih menu: ";
        validasiInput(pilihan);

        switch (pilihan) {
        case 1: inputMenu(); break;
        case 2: updateKetersediaan(); break;
        case 3: inputPesanan(); break;
        case 4: hitungTotalTagihan(); break;
        case 5: tampilkanMenu(); break;
        case 6:
            cout << "Terima kasih telah menggunakan Warteg POS System!\n";
            break;
        default:
            cout << "Pilihan tidak valid. Silakan coba lagi.\n";
        }
    } while (pilihan != 6);
}

int main() {
    menuUtama();
    return 0;
}
