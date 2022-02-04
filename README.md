# sap-api-integrations-pur-info-record-reads-rmq-kube
sap-api-integrations-pur-info-record-reads-rmq-kube は、外部システム(特にエッジコンピューティング環境)をSAPと統合することを目的に、SAP API で購買情報レコードを取得するマイクロサービスです。    
sap-api-integrations-pur-info-record-reads-rmq-kube には、サンプルのAPI Json フォーマットが含まれています。   
sap-api-integrations-pur-info-record-reads-rmq-kube は、オンプレミス版である（＝クラウド版ではない）SAPS4HANA API の利用を前提としています。クラウド版APIを利用する場合は、ご注意ください。   
https://api.sap.com/api/OP_API_INFORECORD_PROCESS_SRV_0001/overview   

## 動作環境  
sap-api-integrations-pur-info-record-reads-rmq-kube は、主にエッジコンピューティング環境における動作にフォーカスしています。  
使用する際は、事前に下記の通り エッジコンピューティングの動作環境（推奨/必須）を用意してください。  
・ エッジ Kubernetes （推奨）  
・ AION のリソース （推奨)  
・ OS: LinuxOS （必須）  
・ CPU: ARM/AMD/Intel（いずれか必須）  
・ RabbitMQ on Kubernetes  
・ RabbitMQ Client

## クラウド環境での利用
sap-api-integrations-pur-info-record-reads-rmq-kube は、外部システムがクラウド環境である場合にSAPと統合するときにおいても、利用可能なように設計されています。  

## RabbitMQ からの JSON Input

sap-api-integrations-pur-info-record-reads-rmq-kube は、Inputとして、RabbitMQ からのメッセージをJSON形式で受け取ります。 
Input の サンプルJSON は、Inputs フォルダ内にあります。  

## RabbitMQ からのメッセージ受信による イベントドリヴン の ランタイム実行

sap-api-integrations-pur-info-record-reads-rmq-kube は、RabbitMQ からのメッセージを受け取ると、イベントドリヴンでランタイムを実行します。  
AION の仕様では、Kubernetes 上 の 当該マイクロサービスPod は 立ち上がったまま待機状態で当該メッセージを受け取り、（コンテナ起動などの段取時間をカットして）即座にランタイムを実行します。　

## RabbitMQ への JSON Output

sap-api-integrations-pur-info-record-reads-rmq-kube は、Outputとして、RabbitMQ へのメッセージをJSON形式で出力します。  
Output の サンプルJSON は、Outputs フォルダ内にあります。  

## RabbitMQ の マスタサーバ環境

sap-api-integrations-pur-info-record-reads-rmq-kube が利用する RabbitMQ のマスタサーバ環境は、[rabbitmq-on-kubernetes](https://github.com/latonaio/rabbitmq-on-kubernetes) です。  
当該マスタサーバ環境は、同じエッジコンピューティングデバイスに配置されても、別の物理(仮想)サーバ内に配置されても、どちらでも構いません。

## RabbitMQ の Golang Runtime ライブラリ
sap-api-integrations-pur-info-record-reads-rmq-kube は、RabbitMQ の Golang Runtime ライブラリ として、[rabbitmq-golang-client](https://github.com/latonaio/rabbitmq-golang-client)を利用しています。

## デプロイ・稼働
sap-api-integrations-pur-info-record-reads-rmq-kube の デプロイ・稼働 を行うためには、aion-service-definitions の services.yml に、本レポジトリの services.yml を設定する必要があります。

kubectl apply - f 等で Deployment作成後、以下のコマンドで Pod が正しく生成されていることを確認してください。
```
$ kubectl get pods
```

## 本レポジトリ が 対応する API サービス
sap-api-integrations-pur-info-record-reads-rmq-kube が対応する APIサービス は、次のものです。

* APIサービス概要説明 URL: https://api.sap.com/api/OP_API_INFORECORD_PROCESS_SRV_0001/overview    
* APIサービス名(=baseURL): API_INFORECORD_PROCESS_SRV

## 本レポジトリ に 含まれる API名
sap-api-integrations-pur-info-record-reads-rmq-kube には、次の API をコールするためのリソースが含まれています。  

* A_PurchasingInfoRecord（購買情報 - 一般）※価格条件関連データを取得するために、ToPurgInfoRecdOrgPlantData、ToPurInfoRecdPrcgCndnValidity、ToPurInfoRecdPrcgCndn、と合わせて利用されます。
* A_PurgInfoRecdOrgPlantData（購買情報 - 購買組織プラント）※価格条件関連データを取得するために、ToPurInfoRecdPrcgCndnValidity、ToPurInfoRecdPrcgCndn、と合わせて利用されます。
* ToPurInfoRecdPrcgCndnValidity（購買情報 - 価格条件存在性）
* ToPurInfoRecdPrcgCndn（購買情報 - 価格条件）
* ToPurgInfoRecdOrgPlantData（購買情報 - 購買組織プラント）

## API への 値入力条件 の 初期値
sap-api-integrations-pur-info-record-reads-rmq-kube において、API への値入力条件の初期値は、入力ファイルレイアウトの種別毎に、次の通りとなっています。  

### SDC レイアウト

* inoutSDC.PurchasingInfoRecord.PurchasingInfoRecord（購買情報）
* inoutSDC.PurchasingInfoRecord.PurchasingOrganizationPlant.PurchasingInfoRecordCategory（購買情報カテゴリ）
* inoutSDC.PurchasingInfoRecord.PurchasingOrganizationPlant.Supplier（仕入先）
* inoutSDC.PurchasingInfoRecord.PurchasingOrganizationPlant.Material（品目）
* inoutSDC.PurchasingInfoRecord.PurchasingOrganizationPlant.PurchasingOrganization（購買組織）
* inoutSDC.PurchasingInfoRecord.PurchasingOrganizationPlant.Plant（プラント）
* inoutSDC.PurchasingInfoRecord.PurchasingOrganizationPlant.MaterialGroup（品目グループ）

## SAP API Bussiness Hub の API の選択的コール

Latona および AION の SAP 関連リソースでは、Inputs フォルダ下の sample.json の accepter に取得したいデータの種別（＝APIの種別）を入力し、指定することができます。  
なお、同 accepter にAll(もしくは空白)の値を入力することで、全データ（＝全APIの種別）をまとめて取得することができます。  

* sample.jsonの記載例(1)  

accepter において 下記の例のように、データの種別（＝APIの種別）を指定します。  
ここでは、"General" が指定されています。    
  
```
	"api_schema": "/sap.s4.beh.purchasinginforecord.v1.PurchasingInfoRecord.Created.v1",
	"accepter": ["General"],
	"purchasing_info_record": "5300000630",
	"deleted": null
```
  
* 全データを取得する際のsample.jsonの記載例(2)  

全データを取得する場合、sample.json は以下のように記載します。  

```
	"api_schema": "/sap.s4.beh.purchasinginforecord.v1.PurchasingInfoRecord.Created.v1",
	"accepter": ["All"],
	"purchasing_info_record": "5300000630",
	"deleted": null
```

## 指定されたデータ種別のコール

accepter における データ種別 の指定に基づいて SAP_API_Caller 内の caller.go で API がコールされます。  
caller.go の func() 毎 の 以下の箇所が、指定された API をコールするソースコードです。  

```
func (c *SAPAPICaller) AsyncGetPurchasingInfoRecord(purchasingInfoRecord, purchasingInfoRecordCategory, supplier, material, purchasingOrganization, plant, materialGroup, conditionType string, accepter []string) {
	wg := &sync.WaitGroup{}
	wg.Add(len(accepter))
	for _, fn := range accepter {
		switch fn {
		case "General":
			func() {
				c.General(purchasingInfoRecord)
				wg.Done()
			}()
		case "Material":
			func() {
				c.Material(purchasingInfoRecord, purchasingInfoRecordCategory, supplier, material, purchasingOrganization, plant)
				wg.Done()
			}()
		case "MaterialGroup":
			func() {
				c.MaterialGroup(purchasingInfoRecord, purchasingInfoRecordCategory, supplier, materialGroup, purchasingOrganization, plant)
				wg.Done()
			}()

		default:
			wg.Done()
		}
	}

	wg.Wait()
}
```

## Output  
本マイクロサービスでは、[golang-logging-library-for-sap](https://github.com/latonaio/golang-logging-library-for-sap) により、以下のようなデータがJSON形式で出力されます。  
以下の sample.json の例は、SAP 購買情報 の 一般データ が取得された結果の JSON の例です。  
以下の項目のうち、"PurchasingInfoRecord" ～ "ToPurgInfoRecdOrgPlantData" は、/SAP_API_Output_Formatter/type.go 内 の Type General {} による出力結果です。"cursor" ～ "time"は、golang-logging-library-for-sap による 定型フォーマットの出力結果です。  

```
{
	"cursor": "/Users/latona2/bitbucket/sap-api-integrations-purchasing-info-record-reads-rmq-kube/SAP_API_Caller/caller.go#L64",
	"function": "sap-api-integrations-purchasing-info-record-reads-rmq-kube/SAP_API_Caller.(*SAPAPICaller).General",
	"level": "INFO",
	"message": [
		{
			"PurchasingInfoRecord": "5300000630",
			"Supplier": "17300001",
			"Material": "MZ-TG-A14",
			"MaterialGroup": "",
			"PurgDocOrderQuantityUnit": "PC",
			"SupplierMaterialNumber": "",
			"SupplierRespSalesPersonName": "",
			"SupplierPhoneNumber": "999 856 4321",
			"SupplierMaterialGroup": "",
			"IsRegularSupplier": true,
			"AvailabilityStartDate": "",
			"AvailabilityEndDate": "",
			"Manufacturer": "",
			"CreationDate": "2018-03-15T09:00:00+09:00",
			"PurchasingInfoRecordDesc": "",
			"LastChangeDateTime": "",
			"IsDeleted": false,
			"to_PurgInfoRecdOrgPlantData": "https://sandbox.api.sap.com/s4hanacloud/sap/opu/odata/sap/API_INFORECORD_PROCESS_SRV/A_PurchasingInfoRecord('5300000630')/to_PurgInfoRecdOrgPlantData"
		}
	],
	"time": "2022-01-28T10:39:56+09:00"
}
```
