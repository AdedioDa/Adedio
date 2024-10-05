# Import library yang dibutuhkan
import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Judul Halaman
st.title("Proyek Analisis Data: Bike Sharing Dataset")
st.subheader("Dashboard Analisis Data")
st.write("Nama: Adedio Daniel Situmeang")
st.write("Email: m284b4ky0081@bangkit.academy")
st.write("ID Dicoding: Adedio Daniel S")

# Deskripsi Pertanyaan Bisnis
st.write("## Pertanyaan Bisnis")
st.write("""
1. Bagaimana pengaruh kondisi cuaca terhadap jumlah total penyewaan sepeda (*cnt*)?
2. Apakah terdapat pola musiman dalam penyewaan sepeda, dan bagaimana distribusi penggunaan sepeda oleh pengguna terdaftar (*registered*) dan pengguna kasual (*casual*) selama periode waktu yang dianalisis?
""")

# Load Data dengan Streamlit File Uploader
uploaded_file = st.file_uploader("Upload file 'day.csv'", type="csv")

if uploaded_file is not None:
    # Membaca data dari file yang diunggah
    data = pd.read_csv(uploaded_file)
    data['dteday'] = pd.to_datetime(data['dteday'])

    # Melakukan Data Wrangling
    # Menambah kolom 'month' dan 'day' dari kolom 'dteday'
    data['month'] = data['dteday'].dt.month
    data['day'] = data['dteday'].dt.day

    # Drop kolom 'instant' yang tidak digunakan
    data = data.drop(['instant'], axis=1)

    # Tampilkan Data Awal
    st.write("### Data Awal: ")
    st.dataframe(data.head())

    # Informasi Dataset
    st.write("### Informasi Dataset: ")
    buffer = data.describe()
    st.dataframe(buffer)

    # Pilihan untuk Eksplorasi Data
    st.write("### Eksplorasi Data Berdasarkan Pilihan:")
    option = st.selectbox('Pilih Analisis yang Ingin Dilihat:', 
                          ('Pengaruh Kondisi Cuaca', 
                           'Pola Musiman Penyewaan Sepeda',
                           'Penyewaan Berdasarkan Tipe Pengguna'))

    # Analisis Berdasarkan Pilihan
    if option == 'Pengaruh Kondisi Cuaca':
        st.write("## Rata-rata Penyewaan Berdasarkan Kondisi Cuaca")

        # Analisis Pengaruh Cuaca terhadap Penyewaan Sepeda
        weather_effect = data.groupby('weathersit').agg({'cnt': 'mean'}).reset_index()

        # Visualisasi
        fig, ax = plt.subplots(figsize=(10, 5))
        sns.barplot(x='weathersit', y='cnt', data=weather_effect, ax=ax)
        ax.set_title('Rata-rata Penyewaan Sepeda Berdasarkan Kondisi Cuaca')
        ax.set_xlabel('Kondisi Cuaca')
        ax.set_ylabel('Rata-rata Jumlah Penyewaan')
        st.pyplot(fig)

        st.write("""
        **Insight:**
        - Cuaca yang lebih baik cenderung memiliki jumlah penyewaan yang lebih tinggi.
        - Cuaca buruk (seperti hujan dan kabut) memiliki jumlah penyewaan yang rendah.
        """)

    elif option == 'Pola Musiman Penyewaan Sepeda':
        st.write("## Rata-rata Penyewaan Berdasarkan Musim")

        # Analisis Pola Musiman Penyewaan Sepeda
        season_effect = data.groupby('season').agg({'cnt': 'mean'}).reset_index()

        # Visualisasi
        fig, ax = plt.subplots(figsize=(10, 5))
        sns.barplot(x='season', y='cnt', data=season_effect, ax=ax)
        ax.set_title('Rata-rata Penyewaan Sepeda Berdasarkan Musim')
        ax.set_xlabel('Musim')
        ax.set_ylabel('Rata-rata Jumlah Penyewaan')
        st.pyplot(fig)

        st.write("""
        **Insight:**
        - Musim panas memiliki rata-rata penyewaan sepeda tertinggi.
        - Musim semi dan gugur memiliki jumlah penyewaan menengah.
        - Musim dingin memiliki penyewaan terendah.
        """)

    elif option == 'Penyewaan Berdasarkan Tipe Pengguna':
        st.write("## Pola Penggunaan Sepeda oleh Pengguna Kasual dan Terdaftar")

        # Analisis Penggunaan Berdasarkan Tipe Pengguna
        user_type_effect = data.groupby('month')[['casual', 'registered']].mean().reset_index()

        # Visualisasi
        fig, ax = plt.subplots(figsize=(15, 5))
        sns.lineplot(x='month', y='casual', data=user_type_effect, label='Pengguna Kasual', ax=ax)
        sns.lineplot(x='month', y='registered', data=user_type_effect, label='Pengguna Terdaftar', ax=ax)
        ax.set_title('Rata-rata Penyewaan Sepeda oleh Pengguna Kasual dan Terdaftar per Bulan')
        ax.set_xlabel('Bulan')
        ax.set_ylabel('Rata-rata Jumlah Penyewaan')
        ax.legend()
        st.pyplot(fig)

        st.write("""
        **Insight:**
        - Pengguna kasual cenderung lebih banyak pada musim panas.
        - Pengguna terdaftar relatif stabil sepanjang tahun.
        """)

    # Footer
    st.write("---")
    st.write("### Kesimpulan:")
    st.write("""
    1. **Kondisi Cuaca** sangat mempengaruhi jumlah penyewaan sepeda. Jumlah penyewaan menurun pada kondisi cuaca yang buruk.
    2. **Pola Musiman** menunjukkan bahwa penyewaan sepeda mencapai puncaknya di musim panas, terutama oleh pengguna kasual.
    3. **Tipe Pengguna**: Pengguna terdaftar cenderung lebih stabil dibandingkan pengguna kasual yang lebih banyak pada musim panas.
    """)
else:
    st.write("Silakan unggah file dataset untuk melanjutkan.")
