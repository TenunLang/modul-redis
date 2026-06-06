# modul-redis

Klien Redis untuk bahasa [Tenun](https://github.com/TenunLang/Tenun), ditulis sepenuhnya dengan Tenun. Memakai protokol RESP.

## Pasang

```
tenun add redis
```

## Pakai

```tenun
impor "redis";

biar r: bulat = redis_sambung("127.0.0.1", 6379);
redis_perintah(r, "SET nama Budi");
cetak(redis_perintah(r, "GET nama"));     // Budi
cetak(redis_perintah(r, "INCR hitung"));  // 1
redis_tutup(r);
```

## Fungsi

- `redis_sambung(inang: teks, port: bulat): bulat` — buka koneksi.
- `redis_tutup(soket: bulat): kosong`
- `redis_perintah(soket: bulat, cmd: teks): teks` — jalankan perintah (dipisah spasi), kembalikan balasan sebagai teks.
- `redis_jalan(soket: bulat, args: []teks): teks` — versi argumen larik (aman untuk nilai berisi spasi).

Balasan RESP dipetakan ke teks: simple string / integer / bulk apa adanya, `nil` menjadi `""`, array digabung dengan baris baru (`\n`).

### Perintah tingkat-tinggi (`src/perintah.tenun`)

```tenun
redis_setex(r, "cache:home", 60, isi);        // cache 60 detik
biar isi: teks = redis_get(r, "cache:home");
redis_hset(r, "user:1", "nama", "Budi");
redis_lpush(r, "antrian", "tugas-1");

// rate limit: maks 100 req / 60 detik per IP
kalau redis_batas(r, "rl:" + ip, 100, 60) == salah { statusKan(429); }
```

- Server: `redis_ping`, `redis_auth`, `redis_pilih`, `redis_bersihkan`.
- String/kunci: `redis_set`, `redis_get`, `redis_setex`, `redis_del`, `redis_ada`, `redis_kedaluwarsa`, `redis_ttl`, `redis_incr`, `redis_decr`, `redis_naik`.
- Hash: `redis_hset`, `redis_hget`, `redis_hdel`, `redis_hsemua`.
- List: `redis_lpush`, `redis_rpush`, `redis_lpop`, `redis_rpop`, `redis_llen`, `redis_lrange`.
- Set: `redis_sadd`, `redis_smembers`, `redis_sada`.
- Rate limit: `redis_batas(soket, kunci, maks, window_detik): bool`.

## Struktur

```
modul-redis/
  tenun.json
  src/
    redis.tenun       koneksi + RESP (kirim/parse) + redis_jalan/redis_perintah
    perintah.tenun    pembungkus SET/GET/HASH/LIST/SET + rate-limit
  contoh/contoh.tenun
  .github/workflows/ci.yml
```

## Catatan

- `terima` membaca dalam satu panggilan; untuk balasan sangat besar mungkin perlu pembacaan bertahap (menyusul).

## Lisensi

MIT.
