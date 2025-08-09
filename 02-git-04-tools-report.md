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
