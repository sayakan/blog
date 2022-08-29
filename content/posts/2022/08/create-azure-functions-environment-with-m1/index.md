---
title: "Azure Functions のローカル開発環境を M1 Mac で構築する"
date: 2022-08-29T09:43:14+09:00
tags: ["Azure Functions", "Python", "node.js"]
---

Azure Functions をローカルで実行したい時、[Azure Functions Core Tools](https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-run-local?tabs=v4%2Cmacos%2Ccsharp%2Cportal%2Cbash#install-the-azure-functions-core-tools) が動かなかったので対応を行った際のメモ。

## install
```cmd
brew tap azure/functions
brew install azure-functions-core-tools@4
# if upgrading on a machine that has 2.x or 3.x installed:
brew link --overwrite azure-functions-core-tools@4
```

## 実行時のエラー
```
Microsoft.Azure.WebJobs.Script: Architecture Arm64 is not supported for language python.
Failed to initialize worker provider for: /opt/homebrew/Cellar/azure-functions-core-tools@4/4.0.4544/workers/python
```

## 対応
```bash
brew install python@3.9
brew uninstall azure-functions-core-tools@4
/opt/homebrew/bin/pip3 install grpcio
npm i -g azure-functions-core-tools@4.0.4483
cp /opt/homebrew/lib/python3.9/site-packages/grpc/_cython/cygrpc.cpython-39-darwin.so ~/.nvm/versions/node/v14.17.6/lib/node_modules/azure-functions-core-tools/bin/workers/python/3.9/OSX/X64/grpc/_cython/cygrpc.cpython-39-darwin.so
```
[ここから抜粋](https://github.com/Azure/azure-functions-core-tools/issues/2834#issuecomment-1136311048)

私は Volta を使っているので、最後のコマンドを以下のように変更
```bash
cd /Users/sayakan/.volta/tools/image/packages/azure-functions-core-tools/lib/node_modules/azure-functions-core-tools/bin/workers/python/3.9/OSX/X64/grpc/_cython/
cp /opt/homebrew/lib/python3.9/site-packages/grpc/_cython/cygrpc.cpython-39-darwin.so .
```

## 終わりに
M1 がリリースされて結構経つのにいまだに対応していないのはなかなか不便だなーと感じている。。

# References
- https://docs.microsoft.com/ja-jp/azure/azure-functions/create-first-function-cli-python?tabs=azure-cli%2Cbash%2Cbrowser