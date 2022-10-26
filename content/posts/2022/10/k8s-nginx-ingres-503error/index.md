---
title: "Ingress Controller デプロイ時に 503 Error が発生した際の対処方法"
date: 2022-10-26T18:06:30+09:00
tags: ["Kubernetes", "Nginx", "Ingress Controller"]
---

# 初めに
以前作成したマニフェストから新しい検証システムを作ろうとして改修したら発生してしまった 503 エラーの対処方法をメモ。
完全に単なる修正漏れだったけど、一瞬焦りました。

# エラー内容
マニフェストを apply 後、ブラウザ上でも `kubectl logs --timestamps=true --tail=100 ingress-nginx-controller-746d65d669-4sdnr` でログでも以下のような 503 エラーが出ていた。

```
2022-10-26T06:16:51.078615118Z xxx.xxx.xxx.xxx - - [26/Oct/2022:06:16:51 +0000] "GET / HTTP/2.0" 503 592 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.0.0 Safari/537.36" 17 0.000 [dev-server-cluster-ip-service-8080] [] - - - - 1458712adb6a97871370b8aa09da6aba
2022-10-26T06:19:42.228297896Z xxx.xxx.xxx.xxx - - [26/Oct/2022:06:19:42 +0000] "GET / HTTP/2.0" 503 592 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.0.0 Safari/537.36" 483 0.000 [dev-server-cluster-ip-service-8080] [] - - - - a97ee6faabaa082347b312beab5ea144
2022-10-26T06:21:49.913053898Z xxx.xxx.xxx.xxx - - [26/Oct/2022:06:21:49 +0000] "GET / HTTP/2.0" 503 592 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.0.0 Safari/537.36" 483 0.000 [dev-server-cluster-ip-service-8080] [] - - - - a7dde1844c7d7c515757588a9f0786f9
2022-10-26T06:21:51.117950898Z xxx.xxx.xxx.xxx - - [26/Oct/2022:06:21:51 +0000] "GET / HTTP/2.0" 503 592 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.0.0 Safari/537.36" 17 0.000 [dev-server-cluster-ip-service-8080] [] - - - - 7bf3d9f22c5a9a956395a57b0ec2e11e
2022-10-26T06:23:09.727619558Z xxx.xxx.xxx.xxx - - [26/Oct/2022:06:23:09 +0000] "GET / HTTP/2.0" 503 592 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.0.0 Safari/537.36" 483 0.000 [dev-server-cluster-ip-service-8080] [] - - - - 04613278459d35ed9907742fb3c76ea6
```

# 原因と解決方法
原因はマニフェストの項目が違うかったこと. 以下（一部抜粋）にサンプルを用意した。Service 上では Selector を `component: server` にしていたが、Development 上では `app: server` と記載していたこと。

- Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: server-cluster-ip-service
spec:
  type: ClusterIP
  selector:
    component: server # here
...
```
- Development
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: server-deployment
  namespace: dev
spec:
  selector:
    matchLabels:
      component: server # ここが app になっていた
  template:
    metadata:
      labels:
        component: server # ここが app になっていた
...
```


# References
> ラベル(Labels) はPodなどのオブジェクトに割り当てられたキーとバリューのペア
> https://kubernetes.io/ja/docs/concepts/overview/working-with-objects/labels/