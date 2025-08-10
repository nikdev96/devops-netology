

# 02‑git‑04‑tools — ответы и как получены

> Репозиторий для анализа: **hashicorp/terraform** (локальная копия). Ниже — краткие ответы и команды, которыми они получены.

---

## Быстрый свод (tl;dr)

- **aefea… →** `aefead2207ef7e2aa5dc81a34aedf0cad4c32545  Update CHANGELOG.md`
- **85024d3 → тег:** `v0.12.23`
- **b8d720 → родители (2):**  
  `56cd7859e05c36c06b56d013b55a252d0bb7e158`,  
  `9ea88f22fc6269854151c571162c5bcf958bee2b`
- **v0.12.23..v0.12.24 → коммиты:** см. ниже
- **Где создана `func providerSource(...)`:**  
  `8c928e83589d90a031f811fae52a81be7153e82f  main: Consult local directories as potential mirrors of providers`
- **Коммиты с изменениями `globalPluginDirs`:**  
  `7c4aeac5f3`, `ffe056bacb`, `22a2580e93`, `35a058fb3d`, `8364383c35`
- **Автор `synchronizedWriters`:** **Martin Atkins**  
  (вводящий коммит: `5ac311e2a91e381e2f52234668b49ba670aa0fe5`)

---

## Подготовка окружения

```bash
git clone https://github.com/hashicorp/terraform.git
cd terraform
```

> Для удобства: отключён пейджер, вывод идёт прямо в терминал.
```bash
export GIT_PAGER=cat; export PAGER=cat
```

---

## 1) Полный хеш и комментарий коммита `aefea…`

**Ответ:** `aefead2207ef7e2aa5dc81a34aedf0cad4c32545  Update CHANGELOG.md`

**Команда:**
```bash
git --no-pager show -s --format='%H  %s' aefea
```

---

## 2) Какому тегу соответствует коммит `85024d3`?

**Ответ:** `v0.12.23`

**Команда:**
```bash
git --no-pager describe --tags --exact-match 85024d3
```

---

## 3) Сколько родителей у `b8d720` и их хеши

**Ответ:** 2 родителя  
`56cd7859e05c36c06b56d013b55a252d0bb7e158`  
`9ea88f22fc6269854151c571162c5bcf958bee2b`

**Команда:**
```bash
git --no-pager show -s --format='%P' b8d720
```

---

## 4) Коммиты между тегами `v0.12.23..v0.12.24`

**Ответ:**
```
33ff1c03bb v0.12.24
b14b74c493 [Website] vmc provider links
3f235065b9 Update CHANGELOG.md
6ae64e247b registry: Fix panic when server is unreachable
5c619ca1ba website: Remove links to the getting started guide's old location
06275647e2 Update CHANGELOG.md
d5f9411f51 command: Fix bug when using terraform login on Windows
4b6d06cc5d Update CHANGELOG.md
dd01a35078 Update CHANGELOG.md
225466bc3e Cleanup after v0.12.23 release
```

**Команда:**
```bash
git --no-pager log --oneline v0.12.23..v0.12.24
```

---

## 5) Где создана `func providerSource(...)`

**Ответ:**  
`8c928e83589d90a031f811fae52a81be7153e82f  main: Consult local directories as potential mirrors of providers`

**Команды:**
```bash
git --no-pager log -S 'func providerSource(' --oneline --reverse -- **/*.go
git --no-pager show -p 8c928e8358
```

---

## 6) Все коммиты, где менялась `globalPluginDirs`

**Ответ:**
```
7c4aeac5f3  stacks: load credentials from config file on startup (#35952)
ffe056bacb  Move command/ to internal/command/
22a2580e93  main: Use the new cliconfig package credentials source
35a058fb3d  main: configure credentials from the CLI config file
8364383c35  Push plugin discovery down into command package
```

**Команда:**
```bash
git --no-pager log -G 'globalPluginDirs' --oneline -- **/*.go
```

---

## 7) Автор функции `synchronizedWriters`

**Ответ:** **Martin Atkins**  
(коммит-вводитель: `5ac311e2a91e381e2f52234668b49ba670aa0fe5  main: synchronize writes to VT100-faker on Windows`)

**Команды:**
```bash
git --no-pager log -S 'synchronizedWriters' --pretty='%H  %an  %ad  %s' --date=short
git --no-pager show -p 5ac311e2a9
```

---

### Примечание
Все команды указаны в «безпейджерном» варианте, чтобы вывод не открывался в редакторе/less.