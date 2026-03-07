# CloudflareとResend による独自ドメインメールアドレス作成記録

## 1. アカウント準備
1. **Gmail作成**: `new_account@gmail.com` を作成。
2. **Cloudflare(new)作成**: `new_account@gmail.com` でアカウント作成。
> [!NOTE]
> 以降、このアカウントを **(new)** と表記。

## 2. ドメイン取得と管理権限の移動
3. **ドメイン購入**: `old_account@gmail.com` **(old)** のアカウントで `new_account.com` を10年分購入。
> [!NOTE]
> 本来 **(new)** で購入すべきところを誤って **(old)** で購入したため、以下の移動作業を実施。
4. **アカウント間移動**: **(old)** から **(new)** へドメインを移動。
5. **支払い登録**: **(new)** でクレジットカードを登録。
6. **DNSSEC有効化**: **(new)** で `new_account.com` のDNSSECを有効化。

## 3. 受信設定 (Email Routing)
7. **Email Routing設定**: `me@new_account.com` のメールを `new_account@gmail.com` に転送するよう設定。

## 4. 送信設定 (Resend)
8. **Resendアカウント作成**: `new_account@gmail.com` で作成。
9. **ドメイン追加**: Resendに `new_account.com` を追加。
10. **DNSレコード追加(DMARC)**: **(new)** にて以下のレコードを追加。

| タイプ | 名前 | コンテンツ | TTL |
| :--- | :--- | :--- | :--- |
| TXT | `_dmarc` | `"v=DMARC1; p=none;"` | 自動 |

11. **API KEY作成**: Resendで `new_account.com` 用のAPI KEYを作成。

## 5. Gmail送信元の設定 (SMTP)
12. **Gmailでの「名前」追加**:
    1. 設定 > アカウントとインポート > 他のメールアドレスを追加。
    2. 以下の情報を入力してウィザードを完了させる。

| 項目 | 設定値 |
| :--- | :--- |
| **名前** | 任意の名前 |
| **メールアドレス** | `me@new_account.com` |
| **エイリアス** | 有効 |
| **SMTPサーバ** | `smtp.resend.com` |
| **ユーザ名** | `resend` |
| **パスワード** | (取得した Resend API KEY) |

## 6. 信頼性向上と疎通確認
13. **Google Postmaster Tools登録**: [Postmaster Tools](https://postmaster.google.com/) にドメインを登録。
14. **所有権確認レコード追加**: **(new)** にて指定のTXTレコードを追加。

| タイプ | 名前 | コンテンツ | TTL |
| :--- | :--- | :--- | :--- |
| TXT | `new_account.com` | (Google指定のコード) | 自動 |

15. **受信確認**: `old_account@gmail.com` → `me@new_account.com` 送信確認。
16. **送信確認**: `me@new_account.com` → `old_account@gmail.com` 送信確認。
> [!TIP]
> 迷惑メールに振り分けられた場合は、手動で「迷惑メールではない」に移動する。

17. **ヘッダー確認**: 「原文を表示」より以下が **PASS** であることを確認。

| SPF | DKIM | DMARC |
| :--- | :--- | :--- |
| PASS | PASS | PASS |

18. **スコア診断**: [Mail-Tester.com](https://www.mail-tester.com) にて診断を実施。
19. **スコア確認**: 減点ポイントがないかチェックする。

## 7. ドメイン・ウォーミングアップ
20. 実際のユーザー（協力者）との間で双方向のメール送受信を行い、レピュテーション（ドメインの評判）の初期構築を実施。
