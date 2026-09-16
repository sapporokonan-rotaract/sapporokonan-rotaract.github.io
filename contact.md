---
layout: page
title: お問い合わせ
permalink: /contact/
---

見学のお申し込み、入会に関するご質問、その他お問い合わせは以下の方法でご連絡ください。

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

<!--
  GitHub Pages は静的サイトのため、サーバー側でフォームの内容を受け取ることができません。
  お問い合わせフォームが欲しい場合は、完全無料の Google フォームを作成し、
  以下のように埋め込むことをおすすめします。

  1. Google フォームで質問項目を作成
  2. 「送信」→「埋め込み <>」タブでHTMLコードを取得
  3. 下記のコメントを外して <iframe> を貼り付け

  <iframe src="https://docs.google.com/forms/d/e/xxxxxxxx/viewform?embedded=true"
          width="100%" height="900" frameborder="0" marginheight="0" marginwidth="0">
    読み込んでいます…
  </iframe>
-->
