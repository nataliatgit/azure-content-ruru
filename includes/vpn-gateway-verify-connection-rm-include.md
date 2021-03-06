### Проверка подключения с помощью портала Azure

VPN-подключение на портале Azure можно проверить, выбрав **Шлюзы виртуальной сети** -> ***имя шлюза*** -> **Параметры** -> **Подключения**. Выбрав имя подключения, можно просмотреть дополнительные сведения в колонке **Подключение**.

### Проверка подключения с помощью PowerShell

Подключение также можно проверить командой *Get-AzureRmVirtualNetworkGatewayConnection –Debug*. В будущем мы создадим соответствующий командлет. Вы можете использовать командлет из следующего примера, заменив значения теми, которые используются у вас. При появлении запроса выберите ответ *A*, то есть All (Все).

	Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 После завершения работы командлета просмотрите результаты, которые он выдал. В следующем примере показано, что подключение установлено (состояние *Connected*), а также указан объем полученных и отправленных данных в байтах.

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }

<!---HONumber=AcomDC_0107_2016-->