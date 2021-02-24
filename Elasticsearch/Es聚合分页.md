

# ES聚合分页

```json
{
  "from": 0,
  "size": 1,
  "query": {
    "match_all": {
      "boost": 1
    }
  },
  "aggregations": {
    "agg": {
      "terms": {
        "field": "pid",
        "size": 10
      },
      "aggregations": {
        "top": {
          "top_hits": {
            "from": 0,
            "size": 1,
            "version": false,
            "seq_no_primary_term": false,
            "explain": false,
            "_source": {
              "includes": [
                "pid"
              ],
              "excludes": [

              ]
            }
          }
        },
        "bucket_field": {
          "bucket_sort": {
            "sort": [

            ],
            "from": 0,
            "size": 10
          }
        }
      }
    }
  }
}
```

```go
// Es中搜索店铺商品（聚合分页版） 
func ShopSearchForEsV2(req *structs.ShopRequest) (map[string]map[string]interface{}, error)  {
	logrus.Info(req.Keywords,req.Page,req.Size)
	if req.Size == 0 {
		req.Size = 10
	}
	if req.Page == 0 {
		req.Page = 1
	}
	from := (req.Page - 1) * req.Size
	// 创建bool查询
	boolQuery := elastic.NewBoolQuery().Must()
	prodNameQuery := elastic.NewMatchQuery("prod_name", req.Keywords)
	prodRemarkQuery := elastic.NewMatchQuery("prod_remark", req.Keywords)
	prodKeywordsQuery := elastic.NewMatchPhraseQuery("prod_keywords", req.Keywords)
	boolQuery.Should(prodNameQuery,prodRemarkQuery, prodKeywordsQuery)

	bucketSort := elastic.NewBucketSortAggregation().From(from).Size(req.Size)
	topHitsAgg := elastic.NewTopHitsAggregation().From(0).Size(6).FetchSource(true)
	aggs := elastic.NewTermsAggregation().Field("shop_id").Size(10).SubAggregation("top", topHitsAgg).SubAggregation("bucket_field",bucketSort)
	ctx := context.Background()
	searchResult, err := es.Client.Search().
		Index("es_tiktok_prod").   // 设置索引名
		Query(boolQuery).   // 设置查询条件
		Aggregation("shop", aggs). // 设置聚合条件，并为聚合条件设置一个名字
		//Sort("prod_id", true). // 设置排序字段，根据Created字段升序排序，第二个参数false表示逆序
		From(0). // 设置分页参数 - 起始偏移量，从第0行记录开始
		Size(1).   // 设置分页参数 - 每页大小
		Do(ctx)             // 执行请求
	if err != nil {
		return nil,err
	}
	var prod model.ProductModel
	var shopMap = make(map[string]map[string]interface{})

	// 使用Terms函数和前面定义的聚合条件名称，查询结果
	agg, found := searchResult.Aggregations.Terms("shop")
	if !found {
		logrus.Error("没有找到聚合数据")
	}

	// 遍历桶数据
	for _, bucket := range agg.Buckets {

		// 每一个桶都有一个key值，其实就是分组的值，可以理解为SQL的group by值
		bucketValue := bucket.Key
		// 打印结果， 默认桶聚合查询，都是统计文档总数
		fmt.Printf("bucket = %q 文档总数 = %d\n", bucketValue, bucket.DocCount)
		shopMap[bucketValue.(string)] = make(map[string]interface{})
		topValue,_ := bucket.Aggregations.TopHits("top")
		var prodList []model.ProductModel
		if topValue.Hits.TotalHits.Value > 0 {
			for _, item := range topValue.Hits.Hits {
				data, _ := item.Source.MarshalJSON()
				// 将json转成struct结果
				json.Unmarshal(data, &prod)
				prodList = append(prodList, prod)
			}
			shopMap[bucketValue.(string)]["prod"] = prodList
		} else {
			shopMap[bucketValue.(string)]["prod"] = []model.ProductModel{}
		}

		// 查询店铺信息
		termQuery := elastic.NewTermQuery("shop_id", bucketValue)
		shopResult, err := es.Client.Search().
			Index("es_tiktok_shop").
			Query(termQuery).
			Do(ctx)
		if err != nil {
			return nil,err
		}
		if shopResult.TotalHits() > 0 {
			// 查询结果不为空，则遍历结果
			var shop model.ShopModel
			if err := json.Unmarshal(shopResult.Hits.Hits[0].Source,&shop); err != nil {
				return nil, err
			}
			//spew.Dump(shop)
			shopMap[bucketValue.(string)]["shop_id"] = shop.ShopID
			shopMap[bucketValue.(string)]["shop_logo"] = shop.ShopLogo
			shopMap[bucketValue.(string)]["shop_name"] = shop.ShopName
			shopMap[bucketValue.(string)]["shop_depict"] = shop.ShopDepict
			shopMap[bucketValue.(string)]["shop_sales"] = shop.ShopSales
			shopMap[bucketValue.(string)]["shop_score"] = shop.ShopScore

		}
	}
	//spew.Dump(shopMap)
	return shopMap,nil
}
```

