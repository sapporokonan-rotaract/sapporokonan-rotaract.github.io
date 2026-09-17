---
layout: page
title: お問い合わせ
permalink: /contact/
---

見学のお申し込み、入会に関するご質問、その他お問い合わせは以下の方法でご連絡ください。

## お問い合わせフォーム

必要事項にご記入のうえ、送信ボタンをおしてください。

<iframe
  src="https://docs.google.com/forms/d/e/1FAIpQLSe8VzGnbDiV6xO2TRMAnK378i9f_It87G93nonMUvDNePuH1w/viewform?embedded=true"
  width="100%"
  height="1000"
  frameborder="0"
  marginheight="0"
  marginwidth="0"
>読み込んでいます…</iframe>

ご入力いただく個人情報の取り扱いについては、[プライバシーポリシー]({{ '/privacy/' | relative_url }})をご確認ください。

## メールでのお問い合わせ

<!-- site.contact.email を _config.yml に設定すると、下のリンクが自動的に有効になります -->
{% if site.contact.email %}
<p><a class="btn btn-primary" href="mailto:{{ site.contact.email }}">{{ site.contact.email }} 宛にメールを送る</a></p>
{% else %}
<p class="empty-note">＜_config.yml の contact.email にメールアドレスを設定してください＞</p>
{% endif %}

## SNSでのお問い合わせ

フッターのリンクからも各SNSをご覧いただけます。SNSのDM等でのお問い合わせも可能です
(利用するSNSと連絡先は <code>_config.yml</code> の <code>contact</code> 項目で設定してください)。
