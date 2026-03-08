# CloudflareとResend による独自ドメインメールアドレス作成記録

## 1. アカウント準備
1. **メールアドレス作成**: `new_account@example.com` を作成。
2. **Cloudflare(new)作成**: `new_account@example.com` でアカウント作成。
> [!NOTE]
> 以降、このアカウントを **(new)** と表記。

## 2. ドメイン取得と管理権限の移動
3. **ドメイン購入**: `old_account@example.com` **(old)** のアカウントで `example2.com` を10年分購入。
> [!NOTE]
> 本来 **(new)** で購入すべきところを誤って **(old)** で購入したため、以下の移動作業を実施。
4. **アカウント間移動**: **(old)** から **(new)** へドメインを移動。
5. **支払い登録**: **(new)** でクレジットカードを登録。
6. **DNSSEC有効化**: **(new)** で `example2.com` のDNSSECを有効化。

## 3. 受信設定 (Email Routing)
7. **Email Routing設定**: `user@example2.com` のメールを `new_account@example.com` に転送するよう設定。

## 4. 送信設定 (Resend)
8. **Resendアカウント作成**: `new_account@example.com` で作成。
9. **ドメイン追加**: Resendに `example2.com` を追加。
10. **DNSレコード追加(DMARC)**: **(new)** にて以下のレコードを追加。

| タイプ | 名前 | コンテンツ | TTL |
| :--- | :--- | :--- | :--- |
| TXT | `_dmarc` | `"v=DMARC1; p=none;"` | 自動 |

11. **API KEY作成**: Resendで `example2.com` 用のAPI KEYを作成。

## 5. Gmail送信元の設定 (SMTP)
12. **Gmailでの「名前」追加**:
    1. 設定 > アカウントとインポート > 他のメールアドレスを追加。
    2. 以下の情報を入力してウィザードを完了させる。

| 項目 | 設定値 |
| :--- | :--- |
| **名前** | 任意の名前 |
| **メールアドレス** | `user@example2.com` |
| **エイリアス** | 有効 |
| **SMTPサーバ** | `smtp.resend.com` |
| **ユーザ名** | `resend` |
| **パスワード** | (取得した Resend API KEY) |

## 6. 信頼性向上と疎通確認
13. **Google Postmaster Tools登録**: [Postmaster Tools](https://postmaster.google.com/) にドメインを登録。
14. **所有権確認レコード追加**: **(new)** にて指定のTXTレコードを追加。

| タイプ | 名前 | コンテンツ | TTL |
| :--- | :--- | :--- | :--- |
| TXT | `example2.com` | (Google指定のコード) | 自動 |

15. **受信確認**: `old_account@example.com` → `user@example2.com` 送信確認。
16. **送信確認**: `user@example2.com` → `old_account@example.com` 送信確認。
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
