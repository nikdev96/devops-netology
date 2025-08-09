# Отчёт по ДЗ: 02-git-04-tools

**Репозитории:**  
- GitHub: https://github.com/nikdev96/devops-netology  
- GitLab: https://gitlab.com/nikdev961/devops-netology

> Ниже — выполненные пункты задания с командами и краткими выдержками результата.

---

## 1) «Красивый» лог (алиасы)
```bash
git config --local alias.lg  "log --oneline --decorate --graph --all"
git config --local alias.lg5 "log --oneline --decorate --graph -n 5"

# проверка
git lg5
```

**Выдержка вывода:**
```
*   bc5a8b1 (main) Merge branch 'demo-merge' (no-ff demo)
|\  
| * 2d636f2 Revert "demo: add temp file for no-ff merge"
| * 2b3b075 demo: add temp file for no-ff merge
|/  
* e120810 git-rebase 1
...
```

---

## 2) Поиск merge-коммитов
```bash
git log --merges --oneline -n 3
```

**Выдержка вывода:**
```
bc5a8b1 Merge branch 'demo-merge' (no-ff demo)
```

---

## 3) grep / pickaxe — где впервые появилась строка
```bash
git grep -n "Next parameter:" -- branching/rebase.sh
```

**Выдержка вывода:**
```
branching/rebase.sh:5:  echo "Next parameter: $param"
```

```bash
git log -S "Next parameter:" --oneline -- branching/rebase.sh
```

**Выдержка вывода:**
```
e120810 git-rebase 1
```

---

## 4) blame — кто внёс конкретную строку
```bash
git blame -L 5,5 -- branching/rebase.sh
```

**Выдержка вывода:**
```
e120810e (nikita 2025-08-09 00:10:55 +0700)   echo "Next parameter: $param"
```

---

## 5) stash — спрятать → проверить → вернуть
```bash
echo "scratch $(date)" >> README.md
git stash push -m "tmp: readme scratch" -- README.md
git stash list
git stash show -p stash@{0}
git stash pop stash@{0}
```

**Фиксация после pop (выдержка лога):**
```
cca4df4 docs: stash demo — scratch line
```

---

## 6) cherry-pick — перенос одного коммита
```bash
# ветка-источник с коммитом
git switch -c tools-cherry
echo "cherry-pick demo $(date)" >> branching/demo.txt
git add branching/demo.txt
git commit -m "tools: cherry-pick demo"

# ветка-получатель от main и перенос
git switch -c tools-target main
git cherry-pick tools-cherry

# проверка результата
git show -1 --name-status
```

**Выдержка вывода:**
```
5c64ec7 tools: cherry-pick demo
A       branching/demo.txt
```

> Ветки `tools-cherry` и `tools-target` запушены в GitHub и GitLab для проверки.

---

### Примечание
Для удобства в этом репозитории отключён пейджер (вывод идёт сразу в терминал), а операции, требующие редактор, выполнялись с `--no-edit` или заранее заданными сообщениями.

---

## Ответы на вопросы по hashicorp/terraform

- **Полный хеш и комментарий для `aefea`:**  
  `aefead2207ef7e2aa5dc81a34aedf0cad4c32545  Update CHANGELOG.md`

- **Какому тегу соответствует `85024d3`:** `v0.12.23`

- **Сколько родителей у `b8d720` и их хеши:** 2  
  `56cd7859e05c36c06b56d013b55a252d0bb7e158`,  
  `9ea88f22fc6269854151c571162c5bcf958bee2b`

- **Коммиты между `v0.12.23` и `v0.12.24`:**
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

- **Коммит, где создана `func providerSource(...)`:**  
  `8c928e83589d90a031f811fae52a81be7153e82f  main: Consult local directories as potential mirrors of providers`

- **Все коммиты, где менялась `globalPluginDirs`:**
```
7c4aeac5f3  stacks: load credentials from config file on startup (#35952)
ffe056bacb  Move command/ to internal/command/
22a2580e93  main: Use the new cliconfig package credentials source
35a058fb3d  main: configure credentials from the CLI config file
8364383c35  Push plugin discovery down into command package
```

- **Кто автор `synchronizedWriters`:** Martin Atkins  
  (коммит-вводитель `5ac311e2a91e381e2f52234668b49ba670aa0fe5`: *main: synchronize writes to VT100-faker on Windows*)
```
