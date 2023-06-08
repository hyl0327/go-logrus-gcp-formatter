# go-logrus-gcp-formatter

[![GoDoc](https://img.shields.io/badge/godoc-reference-blue.svg?style=flat)](https://godoc.org/github.com/joonix/log)

Forked from `joonix/log`. Formatter for logrus, allowing log entries to be
recognized by the fluentd Stackdriver agent on Google Cloud Platform.


## Installation

```
go get github.com/hyl0327/go-logrus-gcp-formatter
```


## Example (using Echo)

```go
package main

import (
	"github.com/labstack/echo/v4"
	"github.com/labstack/echo/v4/middleware"
	log "github.com/sirupsen/logrus"
	gcplog "github.com/hyl0327/go-logrus-gcp-formatter"
)

func main() {
	log.SetFormatter(gcplog.NewFormatter())

	e := echo.New()

	e.Use(middleware.RequestLoggerWithConfig(middleware.RequestLoggerConfig{
		LogError:        true,
		LogStatus:       true,
		LogLatency:      true,
		LogResponseSize: true,
		LogValuesFunc: func(c echo.Context, v middleware.RequestLoggerValues) error {
			l := log.WithField("httpRequest", &gcplog.HTTPRequest{
				Request:      c.Request(),
				Status:       v.Status,
				Latency:      v.Latency,
				ResponseSize: v.ResponseSize,
			})

			if v.Error != nil {
				l.Error(v.Error)
			} else {
				l.Info()
			}

			return nil
		},
	}))

	// ...
}
```
