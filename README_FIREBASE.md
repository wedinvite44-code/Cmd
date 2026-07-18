# 🔥 Panduan Integrasi Firebase Auth (Google Sign-In) — Wedinvite

> File ini berisi panduan lengkap untuk menghubungkan login Google ke project Wedinvite-mu menggunakan **Firebase Authentication**.

---

## 📋 Prasyarat

1. Akun Google (Gmail)
2. Project di [Firebase Console](https://console.firebase.google.com/)
3. Domain/hosting untuk deploy (bisa localhost untuk development)

---

## 🚀 Langkah 1: Buat Project Firebase

1. Buka [https://console.firebase.google.com/](https://console.firebase.google.com/)
2. Klik **"Add project"**
3. Beri nama project: `wedinvite-auth`
4. Matikan Google Analytics (opsional) → klik **Create project**
5. Tunggu sampai selesai → klik **Continue**

---

## 🔧 Langkah 2: Aktifkan Authentication

1. Di sidebar kiri, klik **Build** → **Authentication**
2. Klik **"Get started"**
3. Pilih tab **Sign-in method**
4. Klik **Google** → toggle **Enable**
5. Isi **Support email** dengan emailmu → klik **Save**

---

## 🌐 Langkah 3: Daftarkan Aplikasi Web

1. Di sidebar kiri, klik ikon **</>** (Project settings)
2. Di tab **General**, scroll ke bawah ke bagian **Your apps**
3. Klik ikon **</>** (Web)
4. Beri nama app: `Wedinvite Web`
5. Centang **"Also set up Firebase Hosting"** (opsional)
6. Klik **Register app**
7. **SALIN** kode `firebaseConfig` yang muncul — contohnya:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "wedinvite-auth.firebaseapp.com",
  projectId: "wedinvite-auth",
  storageBucket: "wedinvite-auth.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef123456"
};
```

---

## 💻 Langkah 4: Pasang Firebase SDK ke HTML

Tambahkan script berikut di **<head>** file `index.html`, SEBELUM script JavaScript internal:

```html
<!-- Firebase SDK -->
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-auth-compat.js"></script>
```

---

## 🔑 Langkah 5: Inisialisasi Firebase

Tambahkan kode ini di awal tag `<script>` (sebelum semua fungsi):

```javascript
// GANTI dengan firebaseConfig-mu sendiri dari Firebase Console
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "wedinvite-auth.firebaseapp.com",
  projectId: "wedinvite-auth",
  storageBucket: "wedinvite-auth.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef123456"
};

firebase.initializeApp(firebaseConfig);
const auth = firebase.auth();
const provider = new firebase.auth.GoogleAuthProvider();
```

---

## 🔄 Langkah 6: Ganti Fungsi handleGoogleLogin()

Ganti fungsi `handleGoogleLogin()` yang lama dengan yang baru:

```javascript
function handleGoogleLogin(){
  auth.signInWithPopup(provider)
    .then((result) => {
      const user = result.user;
      console.log("Login berhasil:", user);

      isLoggedIn = true;
      document.getElementById('nav-auth').style.display = 'none';
      document.getElementById('nav-logged').style.display = 'flex';
      document.getElementById('menuBtn').style.display = 'block';

      // Tampilkan nama user di navbar (opsional)
      // document.querySelector('.nav-user-name').textContent = user.displayName;

      goTo('dashboard');
    })
    .catch((error) => {
      console.error("Login gagal:", error);
      alert("Login Google gagal: " + error.message);
    });
}
```

---

## 📤 Langkah 7: Tambahkan Logout Firebase

Ganti fungsi `logout()` dengan:

```javascript
function logout(){
  auth.signOut().then(() => {
    isLoggedIn = false;
    document.getElementById('nav-auth').style.display = 'flex';
    document.getElementById('nav-logged').style.display = 'none';
    document.getElementById('menuBtn').style.display = 'none';
    toggleSidebar();
    goTo('landing');
  }).catch((error) => {
    console.error("Logout gagal:", error);
  });
}
```

---

## 🛡️ Langkah 8: Cek Status Login (Auto-login)

Tambahkan ini di akhir `<script>` agar user tetap login setelah refresh:

```javascript
auth.onAuthStateChanged((user) => {
  if (user) {
    // User sudah login
    isLoggedIn = true;
    document.getElementById('nav-auth').style.display = 'none';
    document.getElementById('nav-logged').style.display = 'flex';
    document.getElementById('menuBtn').style.display = 'block';
  } else {
    // User belum login
    isLoggedIn = false;
    document.getElementById('nav-auth').style.display = 'flex';
    document.getElementById('nav-logged').style.display = 'none';
    document.getElementById('menuBtn').style.display = 'none';
  }
});
```

---

## 🌐 Langkah 9: Atur Authorized Domains

1. Di Firebase Console → Authentication → Settings
2. Tab **Authorized domains**
3. Tambahkan domain tempat kamu deploy:
   - `localhost` (untuk development)
   - `wedinvite.id` (domain production)
   - Domain hosting lainnya

---

## ✅ Langkah 10: Test

1. Buka file `index.html` di browser (bisa langsung klik file)
2. Klik tombol **"Masuk dengan Google"**
3. Pilih akun Google
4. Jika berhasil, akan redirect ke Dashboard
5. Cek **Firebase Console** → Authentication → Users untuk melihat data user yang login

---

## 📁 Struktur File Setelah Integrasi

```
project/
├── index.html          ← File utama (sudah ada)
├── README_FIREBASE.md  ← File ini
└── (hosting files)
```

---

## ⚠️ Catatan Penting

| Hal | Penjelasan |
|-----|-----------|
| **HTTPS Wajib** | Google OAuth memerlukan HTTPS di production. Firebase Hosting sudah include HTTPS gratis. |
| **localhost** | Bisa pakai `localhost` untuk development tanpa HTTPS. |
| **API Key** | Jangan share `apiKey` ke publik. Firebase API Key aman untuk client-side, tapi tetap jaga rahasia project ID. |
| **Biaya** | Firebase Auth gratis untuk < 50,000 user/bulan. Cukup untuk startup. |

---

## 🔗 Resource Tambahan

- [Firebase Auth Docs](https://firebase.google.com/docs/auth)
- [Google Sign-In Web](https://developers.google.com/identity/sign-in/web/sign-in)
- [Firebase Console](https://console.firebase.google.com/)

---

**Selamat! 🎉** Setelah mengikuti panduan ini, Wedinvite-mu sudah punya login Google yang nyata dan terhubung ke akun Gmail.
