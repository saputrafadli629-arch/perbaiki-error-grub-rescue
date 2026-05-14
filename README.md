# perbaiki-error-grub-rescue

# Reinstall GRUB Arch Linux Tanpa Flashdisk

Panduan ini digunakan jika sistem Arch Linux masih bisa masuk sementara melalui `grub rescue`, sehingga reinstall GRUB dapat dilakukan tanpa menggunakan USB installer.

## 1. Masuk ke Linux dari `grub rescue`

Saat berada di:

```bash
grub rescue>

Coba jalankan:

set root=(hd0,gpt5)
set prefix=(hd0,gpt5)/grub
insmod normal
normal

Jika gagal, coba:

set root=(hd0,gpt5)
set prefix=(hd0,gpt5)/boot/grub
insmod normal
normal

Sesuaikan (hd0,gpt5) dengan partisi Linux milikmu.

2. Cek Partisi EFI

Setelah berhasil masuk ke Arch Linux:

lsblk -f

Cari partisi bertipe:

vfat
FAT32

Biasanya ukurannya sekitar 100–500 MB dan digunakan sebagai EFI System Partition.

Contoh:

nvme0n1p1 vfat /boot/efi
3. Mount Partisi EFI (Jika Belum)

Jika partisi EFI belum ter-mount:

sudo mount /dev/nvme0n1p1 /boot/efi

Ganti nvme0n1p1 sesuai partisi EFI milikmu.

4. Reinstall GRUB

Jika EFI berada di /boot/efi:

sudo grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB --recheck

Jika EFI langsung di /boot:

sudo grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB --recheck
5. Generate Ulang Konfigurasi GRUB
sudo grub-mkconfig -o /boot/grub/grub.cfg
6. Membuat EFI Fallback (Opsional tetapi Disarankan)

Windows Update kadang menghapus boot entry GRUB. Untuk mencegahnya:

sudo mkdir -p /boot/efi/EFI/BOOT
sudo cp /boot/efi/EFI/GRUB/grubx64.efi /boot/efi/EFI/BOOT/BOOTX64.EFI

Jika folder GRUB tidak ada, cek nama folder EFI:

ls /boot/efi/EFI

Sesuaikan nama folder jika perlu.

7. Cek Boot Entry EFI

Install efibootmgr jika belum tersedia:

sudo pacman -S efibootmgr

Lalu cek boot entry:

sudo efibootmgr

Pastikan terdapat entry bernama:

GRUB

Jika belum ada, buat manual:

sudo efibootmgr --create \
--disk /dev/nvme0n1 \
--part 1 \
--label "GRUB" \
--loader '\EFI\GRUB\grubx64.efi'

Sesuaikan disk dan partisi dengan sistem milikmu.

8. Reboot
reboot

Jika berhasil, sistem akan boot normal kembali tanpa masuk ke grub rescue.
