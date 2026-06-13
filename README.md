# flutter-aws-lab

AWS の各サービスを Terraform で構築し、Flutter アプリから実際に叩いて挙動を確かめる実験リポジトリ。
SAA の学習がきっかけだが、試験範囲には縛らず「AWS × Flutter で試したいこと」をトピック単位で増やしていく。

学習ログ・記事材料は別リポジトリの `brain/tf/flutter-aws-lab/` に蓄積し、devIO 記事の素材にする。

## 構成

```
flutter-aws-lab/
├── infra/                  # Terraform（トピック単位でスタックを分割）
│   ├── 00-bootstrap/       # tfstate 用 S3 + DynamoDB ロックテーブル（ローカル state）
│   └── 01-cognito/         # Cognito User Pool + App Client（リモート state）
└── app/                    # Flutter アプリ（実験台 / 後で flutter create）
```

- 1 トピック = `infra/<NN>-<topic>/` の 1 スタック = 記事 1 本、という対応にする
- `00-bootstrap` だけはローカル state（state 保管先を作る「鶏と卵」のため）。それ以降は S3 backend のリモート state

## トピック一覧（随時追加）

| NN | トピック | 主なサービス | state |
|----|---------|-------------|-------|
| 00 | bootstrap | S3 / DynamoDB | local |
| 01 | cognito | Cognito User Pool | remote |

## 前提ツール

- Terraform（tfenv 経由でのバージョン管理を推奨）
- AWS CLI v2（認証情報を設定済みであること）
- Flutter / FVM

## apply の流れ

```bash
# 1. state 保管先を作る（初回のみ）
cd infra/00-bootstrap
cp terraform.tfvars.example terraform.tfvars   # 値を埋める
terraform init && terraform apply

# 2. 各トピックを apply（出力されたバケット名を backend に設定）
cd ../01-cognito
cp terraform.tfvars.example terraform.tfvars
terraform init   # backend 設定を聞かれる / backend.tf を編集
terraform apply
```

## 注意

- `*.tfvars` と `*.tfstate` は gitignore 済み（認証情報・実リソース名の漏洩防止）
- 学習用のため、不要になったスタックは `terraform destroy` でこまめに片付ける（課金対策）
# flutter-aws-lab
