# apache2 cgi

- Apache2でCGIを有効化する手順

## 1. CGIモジュールの有効化

- `cgid` モジュールを有効化します
- モジュールの有効化後、`apache2` サービスを再起動します

```
$ sudo a2enmod cgid
Enabling module cgid.
To activate the new configuration, you need to run:
  systemctl restart apache2
$ sudo systemctl restart apache2
$
```

## 2. 任意CGIの設定

- 任意のディレクトリへ配置するCGIを設定します

### 1) 設定ファイル作成

```
$ sudo vim /etc/apache2/conf-available/cgi-script.conf
---------------------------------------------------------------------------------------------------
<IfModule mod_alias.c>
    <IfModule mod_cgi.c>
        Define ENABLE_CGI_SCRIPT
    </IfModule>

    <IfModule mod_cgid.c>
        Define ENABLE_CGI_SCRIPT
    </IfModule>

    <IfDefine ENABLE_CGI_BIN>
        ScriptAlias /cgi-script/ /var/www/cgi-script/
        <Directory "/var/www/cgi-script">
            Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
            AllowOverride None
            Require all granted
        </Directory>
    </IfDefine>
</IfModule>
---------------------------------------------------------------------------------------------------
```

### 2) 設定の有効化

- 設定を有効化後、`apache2` サービスを再起動します

```
$ sudo a2enconf cgi-script
Enabling conf cgi-script.
To activate the new configuration, you need to run:
  systemctl reload apache2
$ sudo systemctl restart apache2
$
```

## 3. CGIの配置

```
$ sudo vim /var/www/cgi-script/index.cgi
---------------------------------------------------------------------------------------------------
#!/usr/local/bin/python3
print('content-type: text/html\n')
print('<html>')
print('<body>')
print('It works in CGI')
print('</body>')
print('</html>')
---------------------------------------------------------------------------------------------------
$
$ sudo chmod 755 /var/www/cgi-script/index.cgi
$ sudo chown www-data:www-data /var/www/cgi-script/index.cgi
```

## 4. 確認

- Webブラウザから `http://web.hoge.com/cgi-script/` へアクセスしてサイトが表示されることを確認します