## PHP Web Shells

1. in burpsuite browser, go to rconfig -> devices -> vendors then add vendor.

```
burpsuite&
```

<p align="center">
  <img src="screenshots/add-vendor.png" alt="add-vendor.png" width="1000">
</p>

2. upload the `webshell.php` from [WhiteWinterWolf](https://github.com/WhiteWinterWolf/wwwolf-php-webshell).

3. once the file is uploaded, there will be a `POST` request from burpsuite. it will seem as the website is hanging (taking forever to load). find `Content-Type` and replace the value from `application/x-php` to `image/gif`. after that, press forward twice.

<p align="center">
  <img src="screenshots/image-gif.png" alt="image-gif.png" width="1000">
</p>

<p align="center">
  <img src="screenshots/php-webshell-upload-success.png" alt="php-webshell-upload-success.png" width="1000">
</p>

4. head to `https://$target_ip/images/vendor/webshell.php` and list files and directories.

```
ls
```