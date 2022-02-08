# zap

## zap 接口

```golang
logger, _ := zap.NewProduction()
logger.Info(msg,zap.String,...)
logger.Error(msg,zap.Error(),zap.String()..)
```

