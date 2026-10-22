import streamlit as st
import pandas as pd
from datetime import date, datetime

# ---------------------------------------------------------
# 1. KONFIGURASI HALAMAN & TEMA VISUAL (UI)
# ---------------------------------------------------------
st.set_page_config(
    page_title="RTQ MASDIK - Sistem Manajemen",
    page_icon="📖",
    layout="wide",
    initial_sidebar_state="expanded"
)

# Custom CSS untuk menyamakan UI dengan aplikasi React Native (Emerald Green)
st.markdown("""
    <style>
    /* Styling Header & Card Utama */
    .main-header {
        background: linear-gradient(135deg, #065F46 0%, #047857 100%);
        padding: 24px;
        border-radius: 16px;
        color: white;
        box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
        margin-bottom: 20px;
    }
    .hero-card {
        background-color: #ECFDF5;
        padding: 20px;
        border-radius: 12px;
        border-left: 6px solid #059669;
        margin-bottom: 20px;
    }
    .stat-card {
        background-color: #FFFFFF;
        padding: 16px;
        border-radius: 12px;
        border: 1px solid #E5E7EB;
        text-align: center;
        box-shadow: 0 1px 3px rgba(0,0,0,0.05);
    }
    /* Button Styling */
    .stButton>button {
        background-color: #059669;
        color: white;
        border-radius: 8px;
        border: none;
        font-weight: 600;
        padding: 8px 16px;
    }
    .stButton>button:hover {
        background-color: #047857;
        color: white;
    }
    /* Hide Streamlit Branding Footer */
    #MainMenu {visibility: hidden;}
    footer {visibility: hidden;}
    </style>
""", unsafe_allow_html=True)

# ---------------------------------------------------------
# 2. SEED DATA / STATE MANAGEMENT
# ---------------------------------------------------------
if 'santri' not in st.session_state:
    st.session_state.santri = [
        {"id": 1, "nama": "Syarifah Balqis", "kategori": "Hafalan", "halaqah": "Aisyah", "status": "Aktif"},
        {"id": 2, "nama": "Syarifah Fauziah Rahmadani M.", "kategori": "Hafalan", "halaqah": "Aisyah", "status": "Aktif"},
        {"id": 3, "nama": "Hafidzah Hajar", "kategori": "Hafalan", "halaqah": "Aisyah", "status": "Aktif"},
        {"id": 4, "nama": "Andi Macica", "kategori": "Hafalan", "halaqah": "Aisyah", "status": "Aktif"},
        {"id": 5, "nama": "Sayid Faiz Muchtar", "kategori": "Hafalan", "halaqah": "Utsman", "status": "Aktif"},
        {"id": 6, "nama": "Muhammad Al Fatih", "kategori": "Hafalan", "halaqah": "Utsman", "status": "Aktif"},
        {"id": 13, "nama": "Muh Al Fatih", "kategori": "Iqra", "halaqah": "Iqra 1", "status": "Aktif"},
        {"id": 14, "nama": "Irwan", "kategori": "Iqra", "halaqah": "Iqra 1", "status": "Aktif"},
        {"id": 15, "nama": "Alfarizki", "kategori": "Iqra", "halaqah": "Iqra 2", "status": "Aktif"},
        {"id": 16, "nama": "Haidar", "kategori": "Iqra", "halaqah": "Iqra 2", "status": "Aktif"},
    ]

if 'presensi' not in st.session_state:
    st.session_state.presensi = []

if 'jurnal' not in st.session_state:
    st.session_state.jurnal = []

# ---------------------------------------------------------
# 3. SIDEBAR NAVIGATION
# ---------------------------------------------------------
with st.sidebar:
    st.markdown("### 📖 RTQ MASDIK")
    st.caption("Rumah Tahfidz Qur'an Masdik")
    st.divider()
    
    menu = st.radio(
        "Menu Utama",
        [
            "🏠 Beranda / Catatan Harian",
            "📋 Presensi Kelas",
            "📚 Jurnal Mutaba'ah",
            "👥 Data Santri",
            "📊 Laporan & Rekap"
        ]
    )
    st.divider()
    st.caption("v1.0.0 • Streamlit Web Edition")

# ---------------------------------------------------------
# 4. HALAMAN 1: BERANDA / DASHBOARD
# ---------------------------------------------------------
if menu == "🏠 Beranda / Catatan Harian":
    st.markdown("""
        <div class="main-header">
            <h1 style='margin:0; font-size: 28px;'>Catatan Harian Guru 🖐️</h1>
            <p style='margin-top:8px; opacity:0.9;'>Kelola presensi santri, jurnal mengajar, dan rekapitulasi harian RTQ MASDIK.</p>
        </div>
    """, unsafe_allow_html=True)
    
    # Hero Call-to-Action
    st.markdown("""
        <div class="hero-card">
            <h3 style='margin:0; color:#065F46;'>Sistem Informasi Manajemen RTQ</h3>
            <p style='margin-top:6px; color:#374151;'>Pilih menu di sebelah kiri untuk menginput presensi harian santri atau mencatat capaian hafalan/Iqra.</p>
        </div>
    """, unsafe_allow_html=True)
    
    # Statistik Singkat
    col1, col2, col3, col4 = st.columns(4)
    with col1:
        st.metric("Total Santri", len(st.session_state.santri))
    with col2:
        hafalan_count = len([s for s in st.session_state.santri if s['kategori'] == 'Hafalan'])
        st.metric("Kelompok Hafalan", hafalan_count)
    with col3:
        iqra_count = len([s for s in st.session_state.santri if s['kategori'] == 'Iqra'])
        st.metric("Kelompok Iqra", iqra_count)
    with col4:
        st.metric("Presensi Hari Ini", len([p for p in st.session_state.presensi if p['tanggal'] == str(date.today())]))

    st.divider()
    st.subheader("📌 Aktivitas Terakhir")
    if st.session_state.jurnal:
        df_recent = pd.DataFrame(st.session_state.jurnal).tail(5)
        st.dataframe(df_recent, use_container_width=True)
    else:
        st.info("Belum ada catatan mutaba'ah yang diinput hari ini.")

# ---------------------------------------------------------
# 5. HALAMAN 2: PRESENSI SANTRI
# ---------------------------------------------------------
elif menu == "📋 Presensi Kelas":
    st.title("📋 Presensi Santri Harian")
    
    c1, c2 = st.columns([1, 2])
    with c1:
        tgl_presensi = st.date_input("Tanggal", date.today())
    with c2:
        kat_filter = st.radio("Kategori Santri", ["Semua", "Hafalan", "Iqra"], horizontal=True)
        
    santri_list = st.session_state.santri
    if kat_filter != "Semua":
        santri_list = [s for s in st.session_state.santri if s['kategori'] == kat_filter]
        
    st.subheader(f"Daftar Hadir ({len(santri_list)} Santri)")
    
    with st.form("form_presensi_rtq"):
        input_data = {}
        for s in santri_list:
            col_nama, col_opt = st.columns([3, 2])
            with col_nama:
                st.write(f"**{s['nama']}**")
                st.caption(f"ID: {s['id']} | Kategori: {s['kategori']} | Halaqah: {s['halaqah']}")
            with col_opt:
                input_data[s['id']] = {
                    "nama": s['nama'],
                    "status": st.selectbox(
                        "Status",
                        ["Hadir", "Izin", "Alfa"],
                        key=f"pres_{s['id']}",
                        label_visibility="collapsed"
                    )
                }
            st.divider()
            
        btn_simpan = st.form_submit_button("💾 Simpan Presensi Hari Ini", use_container_width=True)
        if btn_simpan:
            for s_id, res in input_data.items():
                st.session_state.presensi.append({
                    "tanggal": str(tgl_presensi),
                    "santri_id": s_id,
                    "nama": res['nama'],
                    "status": res['status']
                })
            st.success(f"Presensi tanggal {tgl_presensi} berhasil disimpan!")

# ---------------------------------------------------------
# 6. HALAMAN 3: JURNAL MUTABA'AH
# ---------------------------------------------------------
elif menu == "📚 Jurnal Mutaba'ah":
    st.title("📚 Jurnal Mutaba'ah & Setoran")
    
    with st.form("form_jurnal_mutabaah"):
        santri_pilihan = st.selectbox(
            "Pilih Santri",
            options=[s['nama'] for s in st.session_state.santri]
        )
        tgl_jurnal = st.date_input("Tanggal Setoran", date.today())
        
        col_a, col_b = st.columns(2)
        with col_a:
            keterangan_materi = st.selectbox(
                "Keterangan Progress",
                ["Lanjut", "Mengulang", "Qishhah", "Mewarnai", "Tidak Hadir"]
            )
        with col_b:
            jenis_materi = st.selectbox("Jenis Materi", ["Hafalan Surah/Ayat", "Iqra / Tadarrus"])
            
        materi_capaian = st.text_input("Capaian Detail", placeholder="Contoh: Surah An-Naba 1-15 / Iqra Jilid 3 Hal 14")
        catatan_guru = st.text_area("Catatan Ustaz / Ustazah", placeholder="Masukkan catatan atau evaluasi kelancaran...")
        
        btn_jurnal = st.form_submit_button("💾 Simpan Jurnal Mutaba'ah", use_container_width=True)
        if btn_jurnal:
            st.session_state.jurnal.append({
                "tanggal": str(tgl_jurnal),
                "nama": santri_pilihan,
                "keterangan": keterangan_materi,
                "materi": jenis_materi,
                "capaian": materi_capaian,
                "catatan": catatan_guru
            })
            st.success(f"Catatan mutaba'ah untuk {santri_pilihan} berhasil dicatat!")

# ---------------------------------------------------------
# 7. HALAMAN 4: DATA SANTRI
# ---------------------------------------------------------
elif menu == "👥 Data Santri":
    st.title("👥 Master Data Santri")
    
    df_santri = pd.DataFrame(st.session_state.santri)
    st.dataframe(df_santri, use_container_width=True)
    
    st.subheader("➕ Tambah Santri Baru")
    with st.form("add_santri"):
        c1, c2 = st.columns(2)
        with c1:
            new_nama = st.text_input("Nama Lengkap Santri")
            new_kat = st.selectbox("Kategori", ["Hafalan", "Iqra"])
        with c2:
            new_halaqah = st.text_input("Nama Halaqah / Kelas")
            new_status = st.selectbox("Status", ["Aktif", "Non-Aktif"])
            
        btn_add = st.form_submit_button("Tambah Santri")
        if btn_add and new_nama:
            new_id = max([s['id'] for s in st.session_state.santri]) + 1 if st.session_state.santri else 1
            st.session_state.santri.append({
                "id": new_id,
                "nama": new_nama,
                "kategori": new_kat,
                "halaqah": new_halaqah,
                "status": new_status
            })
            st.success(f"Santri baru '{new_nama}' berhasil ditambahkan!")
            st.rerun()

# ---------------------------------------------------------
# 8. HALAMAN 5: LAPORAN & REKAP
# ---------------------------------------------------------
elif menu == "📊 Laporan & Rekap":
    st.title("📊 Laporan Administrasi RTQ")
    
    tab1, tab2 = st.tabs(["Rekap Presensi", "Rekap Jurnal Mutaba'ah"])
    
    with tab1:
        if st.session_state.presensi:
            df_p = pd.DataFrame(st.session_state.presensi)
            st.dataframe(df_p, use_container_width=True)
            csv_p = df_p.to_csv(index=False).encode('utf-8')
            st.download_button("📥 Export Presensi (CSV)", csv_p, "rekap_presensi_rtq.csv", "text/csv")
        else:
            st.info("Belum ada data presensi yang tersimpan.")
            
    with tab2:
        if st.session_state.jurnal:
            df_j = pd.DataFrame(st.session_state.jurnal)
            st.dataframe(df_j, use_container_width=True)
            csv_j = df_j.to_csv(index=False).encode('utf-8')
            st.download_button("📥 Export Jurnal (CSV)", csv_j, "rekap_jurnal_rtq.csv", "text/csv")
        else:
            st.info("Belum ada data jurnal mutaba'ah yang tersimpan.")
