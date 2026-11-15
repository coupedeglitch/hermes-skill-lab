# hermes-skill-lab

Workspace anker untuk sesi **Skill Lab**: skill yang dipasang di sini **hanya** hidup
di sesi yang dibuka dari folder ini. Skill profil (`~/AppData/Local/hermes/profiles/crypto-jiwa/skills`)
tidak disentuh, jadi sesi lain tidak kena.

## Cara kerja

Hermes memuat skill project dari `<root>/.hermes/skills/` (atau `.agents/skills/`) kalau
root repo-nya ada di `skills.trusted_project_dirs`. Precedence: project > profile > create_dir > external.
Root = folder terdekat yang punya `.git`.

```bash
hermes skills trust "C:/Users/Administrator/hermes-skill-lab"   # sekali per mesin
cd /c/Users/Administrator/hermes-skill-lab
hermes skills list            # kolom Source = "project"
```

Skill project yang isinya kena scanner `skills_guard` dengan verdict **dangerous** akan
dikarantina otomatis (tidak muncul di index, tidak bisa `skill_view`). Itu bukan bug —
jangan diakali dengan `--force` di jalur project.

## Isi

| Skill | Asal | Kenapa |
|---|---|---|
| humanizer | blader/humanizer (MIT) | Hapus pola tulisan AI. Dipakai untuk draf post X. |
| i-have-adhd | ayghri/i-have-adhd (MIT) | Output style: aksi dulu, langkah bernomor. Opsional. |

Skill pihak ketiga tetap milik penulisnya; lisensi di tiap folder.

## Nambah skill baru

Taruh di `.hermes/skills/<nama>/SKILL.md`, commit, push. Skill yang ditulis sendiri
di sesi ini juga mendarat di sini karena `skills.create_dir` mengikuti cwd project.
