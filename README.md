
# Enterprise Branch Network Design: Secure Router-on-a-Stick Architecture

![Topology Overview](link_ke_gambar_topologi_kamu.png)
*Silakan ganti link di atas dengan screenshot topologi fisik dari Cisco Packet Tracer.*

## 📌 Project Overview
Proyek ini adalah implementasi komprehensif dari kurikulum **Cisco CCNA 200-301**. Proyek ini mendemonstrasikan perancangan arsitektur jaringan *enterprise branch* (cabang perusahaan) tradisional menggunakan pendekatan **Router-on-a-Stick (RoaS)** yang dikombinasikan dengan protokol *High Availability* dan skema pengamanan bertingkat.

**Alat yang Digunakan:** Cisco Packet Tracer

## 🚀 Key Technologies & Protocols Implemented
- **Layer 2 (Switching):** VLAN, 802.1Q Trunking, EtherChannel (LACP), Spanning Tree Protocol (STP) Tuning, Port Security (Sticky MAC).
- **Layer 3 (Routing):** Inter-VLAN Routing (RoaS), OSPFv2 (Single Area), HSRP (Gateway Redundancy).
- **Services:** DHCP Server, NAT Overload (PAT).
- **Security:** Standard ACL (VTY Protection), Extended ACL (Server Access Control), SSHv2 (RSA 1024-bit).

## 🏢 Network Architecture & Segmentation
Jaringan dibagi menjadi 4 segmen VLAN utama dan 1 VLAN Management:

| VLAN ID | Department | Network Address   | HSRP Virtual Gateway |
|---------|------------|-------------------|----------------------|
| 10      | IT         | `192.168.10.0/24` | `192.168.10.254`     |
| 20      | HR         | `192.168.20.0/24` | `192.168.20.254`     |
| 30      | Finance    | `192.168.30.0/24` | `192.168.30.254`     |
| 40      | Server     | `192.168.40.0/24` | `192.168.40.254`     |
| 99      | Management | `192.168.99.0/24` | `192.168.99.254`     |

## 🛡️ Security Scenarios & Access Control
Sistem ini menggunakan dua lapis **Access Control List (ACL)** untuk menegakkan kebijakan keamanan perusahaan:

1. **Server Isolation (Extended ACL):** 
   - Departemen **HR** dan **Finance** dilarang berkomunikasi atau mengirim paket ke area Server (VLAN 40).
   - Departemen **IT** memiliki hak akses penuh ke Server.
2. **Infrastructure Management Protection (Standard ACL):**
   - *Remote login* ke infrastruktur (Router/Switch) dipaksa menggunakan **SSH** (Telnet dinonaktifkan).
   - Hanya perangkat dari departemen **IT (VLAN 10)** yang diizinkan untuk membuka sesi SSH ke Router (Gateway). Departemen lain akan ditolak (Connection Refused).

## ⚡ High Availability (HSRP) Testing
Proyek ini mengandalkan **HSRP (Hot Standby Router Protocol)** dengan Router 1 (R1) sebagai *Active* dan Router 2 (R2) sebagai *Standby*.

**Hasil Pengujian Failover:**
Saat link utama ke R1 diputus paksa (simulasi kerusakan *hardware*), R2 sukses mengambil alih peran sebagai *Virtual Gateway* dalam waktu kurang dari 10 detik. Pengguna (PC) tidak mengalami RTO panjang dan jalur *Trace Route* (tracert) otomatis berpindah melewati `192.168.10.2` (R2). Saat R1 kembali menyala, R1 langsung merebut kembali status *Active* berkat fitur `preempt`.

## ⚙️ How to Test (Bagi yang mengunduh file .pkt)
1. Buka file `Project_1_Traditional_RoaS.pkt` di Cisco Packet Tracer.
2. Tunggu indikator port menjadi hijau atau gunakan fitur *Fast Forward Time*.
3. Buka **PC-IT-1**, lakukan `ping 8.8.8.8` (Internet) -> **Berhasil (Reply)**.
4. Buka **PC-HR-1**, lakukan `ping 192.168.40.10` (Server) -> **Ditolak (Destination net unreachable)**.
5. Dari **PC-IT-1**, akses CLI Router lewat SSH: `ssh -l admin 192.168.10.254` (Password: `cisco123`).

---
*Created by [Nama Kamu/Link LinkedIn] as part of Cisco CCNA Portfolio.*
