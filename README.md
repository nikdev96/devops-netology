# devops-netology
## Какие файлы игнорируются (`terraform/.gitignore`)
- всё содержимое каталогов `.terraform/` на любом уровне;
- файлы состояний: `*.tfstate`, `*.tfstate.*`;
- краш-логи: `crash.log`, `crash.*.log`;
- файлы переменных: `*.tfvars`, `*.tfvars.json`;
- override-файлы: `override.tf`, `override.tf.json`, `*_override.tf`, `*_override.tf.json`;
- файл блокировки состояния: `.terraform.tfstate.lock.info`;
- файлы планов: `*.tfplan`, `*.tfplan.*`;
- конфиги CLI: `.terraformrc`, `terraform.rc`.
