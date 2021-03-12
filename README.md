# gomockhandler

**If you find any bugs or have feature requests, please feel free to create an issue.**

gomockhandler is handler of [golang/mock](https://github.com/golang/mock), as the name implies.

`gomockhandler` use one config file to generate all mocks.

With `gomockhandler`, 

- You can generate mocks more **quickly** :rocket:.
- You can check if mock is up-to-date :sparkles:.
- You can manage your mocks in one config file :books:.
- You can generate/edit the config of gomockhandler with CLI :wrench:.

Here is some example of the mock being generated in half the time with `gomockhandler`. (I ran `mockgen` to generate same mocks in `go generate ./...`)


<img width="825" alt="Screen Shot 2021-03-08 at 23 28 46" src="https://user-images.githubusercontent.com/44139130/110334403-1444ba00-8066-11eb-9377-0d8c98a84c9e.png">

![Screen Shot 2021-03-09 at 12 07 03](https://user-images.githubusercontent.com/44139130/110413121-ac778900-80d0-11eb-89c1-73b7e80c11c9.png)


## Background

Some of you may often manage your mocks with `go generate` like below.

```
//go:generate mockgen -source=$GOFILE -destination=mock_$GOFILE -package=$GOPACKAG
```

But, it will take long time to execute `go generate ./...` for projects with many files. 

And we cannot easily check if mock is up-to-date.

`gomockhandler` is created to solve all of these problems.

And you can easily change from managing mocks with `go generate` to managing mocks with gomockhandler.

## Install

You have to install `mockgen` first.

### Go version < 1.16
```
GO111MODULE=on go get github.com/golang/mock/mockgen
GO111MODULE=on go get github.com/sanposhiho/gomockhandler
```
### Go 1.16+
```
go install github.com/golang/mock/mockgen
go install github.com/sanposhiho/gomockhandler
```

## How to use

`gomockhandler` is designed to be **simple** and does only three things.

- generate/edit a config
- generate mock from config
- check if mocks are up-to-date

## generate/edit a config

You need config for `gomockhandler`. 
However, you don't need to generate/edit the config directly, it can be generated/edited from the CLI.

### configuring a new mock

You can configure a new mock to be generated with CLI.

If the config file does not exist, it will be created.

For example, suppose you want to generate the same mock with gomockhandler as the one generated by mockgen below.

```
mockgen -source=foo.go -destination=../mock/
```

The following commands can be used to configure the settings.
As you can see, you just need to add the option `config`.

```
gomockhandler -config=/path/to/config -source=foo.go -destination=../mock/
```

---

You can use all options of mockgen to add a new mock.

`mockgen` has two modes of operation: source and reflect, and gomockhandler support both.

See [golang/mock#running-mockgen](https://github.com/golang/mock#running-mockgen) for more information.

Source mode:
```
gomockhandler -config=/path/to/config -source=foo.go [other options]
```

Reflect mode:
```
gomockhandler -config=/path/to/config [options] database/sql/driver Conn,Driver
```

---

If you use `go:generate` to execute mockgen now, you can generate the config file by rewriting `go:generate` comment a little bit.

replace from `mockgen` to `gomockhandler -config=/path/to/config`, and run `go generate ./...` in your project.

```
- //go:generate mockgen -source=$GOFILE -destination=mock_$GOFILE -package=$GOPACKAG
+ //go:generate gomockhandler -config=/path/to/project -source=$GOFILE -destination=mock_$GOFILE -package=$GOPACKAG
```

After generating the config, your `go:generate` comments are no longer needed. You've been released from a slow-mockgen with `go generate`!

Let's delete all `go:generate` comments for mockgen in your project.

### delete mocks

You can remove the mocks to be generated from the config.

You don't have to specify config option, when you are in project root.

```
gomockhandler -config=gomockhandler.json -destination=./mock/user.go deletemock 
```

## generate mock

You can generate all mocks from config.

You don't have to specify config option, when you are in project root.

```
gomockhandler -config=gomockhandler.json -concurrency=100 mockgen
```

## check if mock is up-to-date

It is useful for ci to check if all mocks are up-to-date

You don't have to specify config option, when you are in project root.

```
gomockhandler -config=gomockhandler.json -concurrency=100 check
```
If some mocks are not up to date, you can see the error and `gomockhandler` will exit with exit-code 1 

```
2021/03/10 22:17:12 [WARN] mock is not up to date. source: ./interfaces/user.go, destination: ./interfaces/../mock/user.go
2021/03/10 22:17:12 mocks is not up-to-date
```

