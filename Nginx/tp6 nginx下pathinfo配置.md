# tp6 nginx下pathinfo配置

```nginx
if (!-e $request_filename) {
      rewrite  ^/index.php(.*)$  /index.php?s=/$1  last;
      rewrite  ^(.*)$  /index.php?s=/$1  last;
      break;
}
```

