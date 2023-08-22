# terraform_circleci
## 要件
- CircleCIを用いてTerraformのCI/CD環境を構築する
- CI：　terraform validate/plan
- CD: terraform apply
- OrbsのTerraformを利用

## テスト証跡
### tfファイル変更
```terraform:aws_eip.tf
# EIP割り当て
resource "aws_eip" "menta_vpc_eip" {
  depends_on = [aws_internet_gateway.menta_igw]
  vpc      = true
}

# CircleCIテスト用にEIP追加
resource "aws_eip" "circleci_test" {
  depends_on = [aws_internet_gateway.menta_igw]
  vpc      = true
}
```

### リモートリポジトリのcircleci_testブランチへgit push
```
fukuiryousukenoMacBook-Pro:terraform fukuiryousuke$ git add aws_eip.tf
fukuiryousukenoMacBook-Pro:terraform fukuiryousuke$ git commit -m "modify: aws_eip.tf"
[circleci_test 08bf036] modify: aws_eip.tf
 1 file changed, 6 insertions(+)
fukuiryousukenoMacBook-Pro:terraform fukuiryousuke$ git push circleci_test
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 4 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 499 bytes | 499.00 KiB/s, done.
Total 4 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To github.com:pileray/circlrci_test.git
   1f665af..08bf036  circleci_test -> circleci_test
```

### CI環境で`terraform validate`と`terraform plan`を実行
![image](https://user-images.githubusercontent.com/60649665/181866528-2df9c0ff-89db-46d5-ba3c-9deab3b89de6.png)
![image](https://user-images.githubusercontent.com/60649665/181866588-f3396ff8-ce9c-4d05-8cb5-5f94ec0cdbc1.png)

### mainブランチへマージ
![image](https://user-images.githubusercontent.com/60649665/181866916-ba634853-1de2-4eb0-a353-507fed0d9ff7.png)

### CD環境で`terraform apply`を実行
![image](https://user-images.githubusercontent.com/60649665/181866959-4e71115e-f342-4313-b6a8-9fb69d129311.png)
![image](https://user-images.githubusercontent.com/60649665/181866981-78d3d181-9472-4a4f-bf80-d0a1a8c6fe1b.png)


### コンソールでの確認
#### mainマージ前
![image](https://user-images.githubusercontent.com/60649665/181866697-7bf53814-9d95-43f2-bf1b-faab07b9ffc1.png)

#### mainマージ後
![image](https://user-images.githubusercontent.com/60649665/181867023-66d4e7df-2edd-4a05-848f-18de9f1f750d.png)

### ダミーファイルとして ./circleci/circleci_test を作成しgit push
```
fukuiryousukenoMacBook-Pro:.circleci fukuiryousuke$ git add circleci_test
fukuiryousukenoMacBook-Pro:.circleci fukuiryousuke$ git commit -m "circleci test for path-filtering"
[circleci_test 8750dec] circleci test for path-filtering
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 .circleci/circleci_test
fukuiryousukenoMacBook-Pro:.circleci fukuiryousuke$ git push circleci_test
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 4 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 402 bytes | 402.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To github.com:pileray/circlrci_test.git
   08bf036..8750dec  circleci_test -> circleci_test
```

### terraformディレクトリ外の変更のためpath-filteringのみ実行される
![image](https://user-images.githubusercontent.com/60649665/181867201-e14e4493-a85b-45eb-8270-9d30b828d56e.png)
