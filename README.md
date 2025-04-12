#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

typedef struct {
    char nama[50];
    int harga;
    int jumlah;
    float diskon;
    int total;
} Produk;

void clear_screen() {
    #ifdef _WIN32
        system("cls");
    #else
        system("clear");
    #endif
}

void tampilkan_menu() {
    printf("==============================================\n");
    printf("         Selamat Datang di Toko SKENSA\n");
    printf("==============================================\n");
    printf("| No | Barang          | Harga               |\n");
    printf("==============================================\n");
    printf("| 1  | Buku Tulis      | Rp. 5000            |\n");
    printf("| 2  | Pensil          | Rp. 2000            |\n");
    printf("| 3  | Penghapus       | Rp. 1000            |\n");
    printf("| 4  | Penggaris       | Rp. 1000            |\n");
    printf("| 5  | Bujur Sangkar   | Rp. 500             |\n");
    printf("==============================================\n");
    printf("99. Struk Pembayaran\n");
    printf("55. Reset\n");
    printf("00. Keluar\n");
    printf("==============================================\n");
}

float hitung_diskon(int jumlah) {
    if (jumlah >= 5) return 0.15;
    else if (jumlah >= 3) return 0.10;
    return 0.0;
}

void urutkan_produk(Produk *produk, int n) {
    for (int i = 0; i < n - 1; i++) {
        for (int j = i + 1; j < n; j++) {
            if (produk[j].jumlah > produk[i].jumlah) {
                Produk temp = produk[i];
                produk[i] = produk[j];
                produk[j] = temp;
            }
        }
    }
}

void buat_nama_file(char *filename) {
    time_t t = time(NULL);
    struct tm *tm_info = localtime(&t);
    strftime(filename, 100, "struk_%Y%m%d_%H%M%S.txt", tm_info);
}

// ? Tambahkan waktu di struk file
void simpan_struk(Produk *produk, int n, int total_harga, int total_diskon, int bayar) {
    char filename[100];
    buat_nama_file(filename);
    FILE *fp = fopen(filename, "w");

    int tagihan = total_harga - total_diskon;
    int kembalian = bayar - tagihan;

    // Tampilkan tanggal dan waktu
    time_t t = time(NULL);
    struct tm *tm_info = localtime(&t);
    char waktu[100];
    strftime(waktu, sizeof(waktu), "%A, %d-%m-%Y %H:%M:%S", tm_info);
    
    fprintf(fp, "======================= STRUK TOKO SKENSA =======================\n");
    fprintf(fp, "| Barang             | Jumlah | Harga  | Total     | Diskon    |\n");
    fprintf(fp, "=================================================================\n");

    for (int i = 0; i < n; i++) {
        if (produk[i].jumlah > 0) {
            fprintf(fp, "| %-18s | %-6d | %-6d | %-9d | %-8d |\n",
                    produk[i].nama,
                    produk[i].jumlah,
                    produk[i].harga,
                    produk[i].total,
                    (int)(produk[i].diskon * produk[i].total));
        }
    }

    fprintf(fp, "=================================================================\n");
    fprintf(fp, "Total Harga : Rp. %d\n", total_harga);
    fprintf(fp, "Total Diskon: Rp. %d\n", total_diskon);
    fprintf(fp, "Tagihan     : Rp. %d\n", tagihan);
    fprintf(fp, "Pembayaran  : Rp. %d\n", bayar);
    fprintf(fp, "Kembalian   : Rp. %d\n", kembalian);
    fprintf(fp, "Tanggal dan Waktu: %s\n", waktu); 
    fprintf(fp, "=================================================================\n");

    fclose(fp);
    printf("Struk berhasil disimpan sebagai: %s\n", filename);
}


void tampilkan_struk(Produk *produk, int n, int total_harga, int total_diskon, int bayar) {
    int tagihan = total_harga - total_diskon;
    int kembalian = bayar - tagihan;

    time_t t = time(NULL);
    struct tm *tm_info = localtime(&t);
    char waktu[100];
    strftime(waktu, sizeof(waktu), "%A, %d-%m-%Y %H:%M:%S", tm_info);

    printf("======================= STRUK TOKO SKENSA =======================\n");
    printf("| Barang             | Jumlah | Harga  | Total     | Diskon    |\n");
    printf("=================================================================\n");

    for (int i = 0; i < n; i++) {
        if (produk[i].jumlah > 0) {
            printf("| %-18s | %-6d | %-6d | %-9d | %-8d |\n",
                   produk[i].nama,
                   produk[i].jumlah,
                   produk[i].harga,
                   produk[i].total,
                   (int)(produk[i].diskon * produk[i].total));
        }
    }

    printf("=================================================================\n");
    printf("Total Harga : Rp. %d\n", total_harga);
    printf("Total Diskon: Rp. %d\n", total_diskon);
    printf("Tagihan     : Rp. %d\n", tagihan);
    printf("Pembayaran  : Rp. %d\n", bayar);
    printf("Kembalian   : Rp. %d\n", kembalian);
    printf("Tanggal dan Waktu: %s\n", waktu); 
    printf("=================================================================\n");
}
	
int main() {
    Produk produk[5] = {
        {"Buku Tulis", 5000, 0, 0, 0},
        {"Pensil", 2000, 0, 0, 0},
        {"Penghapus", 1000, 0, 0, 0},
        {"Penggaris", 1000, 0, 0, 0},
        {"Bujur Sangkar", 500, 0, 0, 0}
    };

    int pilihan, bayar;
    int total_harga, total_diskon;

    while (1) {
        clear_screen();
        tampilkan_menu();
        printf("Masukkan pilihan: ");
        scanf("%d", &pilihan);

        if (pilihan >= 1 && pilihan <= 5) {
            printf("Masukkan jumlah %s: ", produk[pilihan - 1].nama);
            scanf("%d", &produk[pilihan - 1].jumlah);

            produk[pilihan - 1].diskon = hitung_diskon(produk[pilihan - 1].jumlah);
            produk[pilihan - 1].total = produk[pilihan - 1].harga * produk[pilihan - 1].jumlah;

        } else if (pilihan == 99) {
            total_harga = 0;
            total_diskon = 0;

            for (int i = 0; i < 5; i++) {
                if (produk[i].jumlah > 0) {
                    total_harga += produk[i].total;
                    total_diskon += (int)(produk[i].diskon * produk[i].total);
                }
            }

            int tagihan = total_harga - total_diskon;

            urutkan_produk(produk, 5);
            printf("\nTotal yang harus dibayar: Rp. %d\n", tagihan);

            int total_bayar = 0;
            while (total_bayar < tagihan) {
                int tambahan;
                printf("Masukkan pembayaran (sisa tagihan: Rp. %d): Rp. ", tagihan - total_bayar);
                scanf("%d", &tambahan);
                total_bayar += tambahan;

                if (total_bayar < tagihan) {
                    printf("Uang masih kurang, sisa kekurangan: Rp. %d\n", tagihan - total_bayar);
                }
            }
            bayar = total_bayar;

            clear_screen();
            tampilkan_struk(produk, 5, total_harga, total_diskon, bayar);
            simpan_struk(produk, 5, total_harga, total_diskon, bayar);

            printf("\nTekan Enter untuk kembali ke menu...");
            getchar(); getchar();

        } else if (pilihan == 55) {
            for (int i = 0; i < 5; i++) {
                produk[i].jumlah = 0;
                produk[i].total = 0;
                produk[i].diskon = 0;
            }
            printf("Data berhasil di-reset. Tekan Enter untuk melanjutkan...");
            getchar(); getchar();

        } else if (pilihan == 0) {
            printf("Terima kasih telah berbelanja!\n");
            break;

        } else {
            printf("Pilihan tidak valid! Tekan Enter untuk melanjutkan...");
            getchar(); getchar();
        }
    }

    return 0;
}
