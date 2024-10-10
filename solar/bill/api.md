

## 电价管理

### 导入接口

POST `/bill/tariff/import`
`content-type: multipart/form-data`

body: `file, note`

### 页面修改接口

POST `/bill/tariff/update`

body

```json5
{
    "onGrid": [
        {
            "siteId": "siteId",
            // "fixed:1.33" 或者 "realtime"
            "conf": "fixed:1.33"
        }
    ],
    "offGridProvince": [
        {
            "provinceId": "provinceId",
            "price": {
                "001": 1.2,
                "002": 1.3,
                "003": 0.5
            },
            "yyyyMM": "202001",
            //1:单一制, 2:两部制
            "billing_mode": "1",
            //1:20kV以下, 2:35kV, 3:110kV, 4:220kV以上
            "voltage_level": "1"
        }
    ],
    "offGridSite": [
        {
            "siteId": "siteId",
            "price": {
                "001": 1.2,
                "002": 1.3,
                "003": 0.5
            },
            "yyyyMM": "202001"
        }
    ],
    "selfCons": [
        {
            "siteId": "siteId",
            "beginDate": "20200101",
            "endDate": "20201231",
            //fixed/discount
            "type": "discount",
            //type为fixed时，才需要
            "price": 1.2,
            //type为discount时，才需要
            "discountFactor": 0.8,
            //type为discount时，才需要
            "minLimit": 0.3,
            //type为discount时，才需要
            "maxLimit": 0.8
        }
    ],
    "consSite": [{
        "siteId": "siteId",
        "beginDate": "20200101",
        "endDate": "20201231",
        //fixed/off_grid
        "type": "off_grid",
        //type为fixed时，才需要
        "price": 1.2
    }]
}

```

response

```json5
{
    "code": "10000",
    "data": true
}
```

### 查询当前电价配置接口

GET `/bill/tariff/get?siteId=siteId`

response

```json5
{
    "code": "10000",
    "data": {
        "offGridSite": [
            {
                "siteId": "siteId",
                "price": {
                    "001": 1.2,
                    "002": 1.3,
                    "003": 0.5
                },
                "yyyyMM": "202001"
            }
        ],
        "selfCons": [
            {
                "siteId": "siteId",
                "beginDate": "20200101",
                "endDate": "20201231",
                "type": "fixed",
                "price": 1.2
            }
        ],
        "consSite": [
            {
                "siteId": "siteId",
                "beginDate": "20200101",
                "endDate": "20201231",
                "type": "fixed",
                "price": 1.2
            }
        ]
    }
}
```

### 批量查询是否存在电价配置接口

GET `/bill/tariff/exist?siteIds=siteId1,siteId2&begin=202001&end=202002`

response

```json5
{
    "code": "10000",
    "data": {
        "site1": {
            "202001": true,
            "202002": true
        }
    }
}
```

### 历史记录接口

GET `/bill/tariff/history`

response

```json5
{
    "code": "10000",
    "data": [
        {
            "batchId": 11,
            "updateTime": 1727430365000,
            "updateUser": "admin",
            //'1:页面修改 2:导入修改'
            "type": 1,
            "note": "note",
            "file_path": "file_path"
        }
    ]
}
```

### 历史记录导出接口

GET `/bill/tariff/exportHistory?batchId=11`

### 当前配置导出接口

GET `/bill/tariff/exportSite?siteId=siteId`

### 模版导出接口

GET `/bill/tariff/exportTemplate?types=1,2,3&siteIds=siteId1,siteId2`
> 1: provinceOffGrid , 2:siteOffGrid , 3: selfCons, 4: consSite


## 电费账单

### 创建账单接口

POST `/bill/create`

body

```json5
{
    "siteId": "siteId",
    "yyyymm": "202001",
    "lastMeteringTime": "2020-01-01 00:00",
    "meteringTime": "2020-02-01 00:00",
    "note": "note"
}
```

response

```json5
{
    "code": "10000",
    "data": true
}
```

### 账单列表接口

GET `/bill/list?siteIds=id1,id2&begin=202001&end=202002`

response

```json5
{
    "code": "10000",
    "data": [
        {
            "id": 1,
            "siteId": "siteId",
            "yyyymm": "202001",
            "lastMeteringTime": "2020-01-01 00:00",
            "meteringTime": "2020-02-01 00:00",
        }
    ]
    // 根据场站、月份排序
}
```

### 账单导出接口
GET `/bill/export?id=id&type=quantity`

### 导入电量确认单接口(本期不做)
POST `/bill/import`
`content-type: multipart/form-data`

body: `id, file`

### 编辑账单接口

POST `/bill/edit?id=id&updateTariff=true`

body

```json5
{
    "productionName": "电量单名称",
    "billName": "账单名称",
    "meteringUser": "username",
    "note": "note",
    "adjustment": 100,
    //全量更新
    "quantityBase": {
        "gmt": [
            {
                "assetId": "assetId",
                "prod": {
                    "sum": {
                        "lastValue": 200,
                        "value": 240,
                    },
                    "001": {
                        "lastValue": 100,
                        "value": 120
                    },
                    "002": {
                        "lastValue": 100,
                        "value": 120
                    }
                },
                "cons": {
                    "sum": {
                        "lastValue": 200,
                        "value": 240
                    },
                    "001": {
                        "lastValue": 100,
                        "value": 120
                    }
                }
            }
        ],
        "emt": [
            {
                "assetId": "assetId",
                "prod": {
                    "sum": {
                        "lastValue": 200,
                        "value": 240
                    },
                    "001": {
                        "lastValue": 100,
                        "value": 120
                    },
                    "002": {
                        "lastValue": 100,
                        "value": 120
                    }
                }
            }
        ]
    }
}
```

### 电量确认单详情

GET `/bill/quantity?id=id`

response

```json5
{
    "code": "10000",
    "data": {
        "gmt": [
            {
                "assetId": "assetId",
                "scale": 100,
                "nameI18n": {
                    "defaultValue": "name",
                    "i18nValue": {
                        "zh_CN": "中文",
                        "en_US": "英文"
                    }
                },
                "prod": {
                    "sum": {
                        "lastValue": 200,
                        "value": 240,
                        "quantity": 4000,
                        //开启了分时电量指标计算才有
                        "metric": 4000
                    },
                    "001": {
                        "lastValue": 100,
                        "value": 120,
                        "quantity": 2000,
                        "metric": 2000
                    },
                    "002": {
                        "lastValue": 100,
                        "value": 120,
                        "quantity": 2000,
                        "metric": 2000
                    }
                },
                "cons": {
                    "sum": {
                        "lastValue": 200,
                        "value": 240,
                        "quantity": 4000,
                        "metric": 4000
                    },
                    "001": {
                        "lastValue": 100,
                        "value": 120,
                        "quantity": 2000,
                        "metric": 2000
                    },
                    "002": {
                        "lastValue": 100,
                        "value": 120,
                        "quantity": 2000,
                        "metric": 2000
                    }
                }
            }
        ],
        "emt": [
            {
                "assetId": "assetId",
                "scale": 100,
                "nameI18n": {
                    "defaultValue": "name",
                    "i18nValue": {
                        "zh_CN": "中文",
                        "en_US": "英文"
                    }
                },
                "prod": {
                    "sum": {
                        "lastValue": 200,
                        "value": 240,
                        "quantity": 4000
                    },
                    "001": {
                        "lastValue": 100,
                        "value": 120,
                        "quantity": 2000
                    },
                    "002": {
                        "lastValue": 100,
                        "value": 120,
                        "quantity": 2000
                    }
                }
            }
        ],
        "sumQuantity": {
            "prod": {
                "sum": 8000,
                "001": 2000,
                "002": 6000
            },
            "cons": {
                "sum": 8000,
                "001": 2000,
                "002": 6000
            },
            "onGrid": {
                "sum": 8000,
                "001": 2000,
                "002": 6000
            }
        }
    }
}
```

### 电费结算单详情

GET `/bill/fee?id=id`

response

```json5
{
    "code": "10000",
    "data": {
        "quantity": {
            "gmt": [
                {
                    "assetId": "assetId",
                    "scale": 100,
                    "nameI18n": {
                        "defaultValue": "name",
                        "i18nValue": {
                            "zh_CN": "中文",
                            "en_US": "英文"
                        }
                    },
                    "prod": {
                        "sum": {
                            "lastValue": 200,
                            "value": 240,
                            "quantity": 4000,
                            "metric": 4000
                        },
                        "001": {
                            "lastValue": 100,
                            "value": 120,
                            "quantity": 2000,
                            //开启了分时电量指标计算才有
                            "metric": 2000
                        },
                        "002": {
                            "lastValue": 100,
                            "value": 120,
                            "quantity": 2000,
                            "metric": 2000
                        }
                    },
                    "cons": {
                        "sum": {
                            "lastValue": 200,
                            "value": 240,
                            "quantity": 4000,
                            "metric": 4000
                        },
                        "001": {
                            "lastValue": 100,
                            "value": 120,
                            "quantity": 2000,
                            "metric": 2000
                        },
                        "002": {
                            "lastValue": 100,
                            "value": 120,
                            "quantity": 2000,
                            "metric": 2000
                        }
                    }
                }
            ],
            "emt": [
                {
                    "assetId": "assetId",
                    "scale": 100,
                    "nameI18n": {
                        "defaultValue": "name",
                        "i18nValue": {
                            "zh_CN": "中文",
                            "en_US": "英文"
                        }
                    },
                    "prod": {
                        "sum": {
                            "lastValue": 200,
                            "value": 240,
                            "quantity": 4000
                        },
                        "001": {
                            "lastValue": 100,
                            "value": 120,
                            "quantity": 2000
                        },
                        "002": {
                            "lastValue": 100,
                            "value": 120,
                            "quantity": 2000
                        }
                    }
                }
            ],
            "sumQuantity": {
                "prod": {
                    "sum": 8000,
                    "001": 2000,
                    "002": 6000
                },
                "cons": {
                    "sum": 8000,
                    "001": 2000,
                    "002": 6000
                },
                "onGrid": {
                    "sum": 8000,
                    "001": 2000,
                    "002": 6000
                }
            }
        },
        "selfConsFee": {
            "discountFactor": 0.4,
            "items": {
                "001": {
                    "quantity": 2000,
                    "offGridPrice": 1.2,
                    "settlementPrice": 0.8,
                    "offGridPriceFee": 2400,
                    "settlementPriceFee": 1600
                }
            },
            "sum": {
                "offGridPriceFee": 2400,
                "settlementPriceFee": 1600
            }
        },
        "consFee": {
            "items": {
                "001": {
                    "quantity": 2000,
                    "offGridPrice": 1.2,
                    "settlementPrice": 0.8,
                    "offGridPriceFee": 2400,
                    "settlementPriceFee": 1600
                }
            },
            "sum": {
                "offGridPriceFee": 2400,
                "settlementPriceFee": 1600
            }
        },
        "sumFee": {
            //结算电费
            "settlement": 1234,
            //调整电费
            "adjustment": -100,
            //实际电费
            "total": 1134,
            //节省电费
            "save": 123,
            //自用率
            "selfUseRate": 0.9
        },
        "note": "note"
    }
}
```
