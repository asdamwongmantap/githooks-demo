# githooks-demo

Demo Git hooks dengan Golang. Satu program Go (`githook`) dipanggil Git pada tiga momen:

| Hook         | Kapan                | Yang diperiksa                                  |
| ------------ | -------------------- | ----------------------------------------------- |
| `pre-commit` | sebelum commit       | format (`gofmt`) dan `go vet`                   |
| `commit-msg` | pesan commit ditulis | format [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) |
| `pre-push`   | sebelum push         | `go test ./...`                                 |

Hook gagal (exit code bukan 0) = aksi Git dibatalkan.

## Prasyarat

- Go 1.22 atau lebih baru
- Git
- Windows: gunakan Git Bash (shim hook memakai `sh`)

## Pemasangan

```sh
go run ./cmd/githook install
```

Perintah ini membuat shim di `.githooks/` dan menjalankan `git config core.hooksPath .githooks`.
Pengaturan `core.hooksPath` bersifat lokal, jadi setiap orang yang meng-clone repo perlu menjalankannya sekali.

## Struktur

```
cmd/githook/        program hook (main.go, precommit.go, commitmsg.go, prepush.go)
internal/calc/      kode contoh + test sebagai bahan pemeriksaan
.githooks/          shim hook (dibuat oleh `githook install`)
docs/               materi tutorial (PDF)
```

## Contoh format pesan commit

```
feat(auth): tambah login dengan Google
fix(cart): hitung total dengan diskon
docs: perbarui README
feat(api)!: ubah format respons
```

Type yang diizinkan: `feat fix docs style refactor perf test build ci chore revert`.

## Melewati hook

```sh
git commit --no-verify
git push --no-verify
```

Hook lokal hanyalah alat kenyamanan, bukan penjaga keamanan. Ulangi pemeriksaan yang sama di CI (lihat Tahap 9 di tutorial).

## Pemecahan masalah

- Hook tidak jalan: cek `git config core.hooksPath` (harus `.githooks`) dan `chmod +x .githooks/*`.
- `go: command not found` saat commit dari aplikasi GUI: commit dari terminal.
- Hook terasa lambat: `go run` mengompilasi tiap kali; build sekali ke binary lalu panggil dari shim.

## Dokumentasi

Tutorial lengkap (Indonesia): [docs/Git Hooks Automation Golang Webinar.pdf](docs/Git%20Hooks%20Automation%20Golang%20Webinar.pdf)
