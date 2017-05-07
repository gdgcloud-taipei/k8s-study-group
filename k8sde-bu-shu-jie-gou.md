# K8S的部屬結構

K8S在架構上透過Pod來封裝一個以上的container作為部署的最小單位，並且透過deployment來封裝pod，讓pod本身可以被監控與執行scale或rolling update等動作，整體的概念如下圖所示：

![](/assets/gke-concept.png)

* Pod封裝container作為部署的最小單位
* 透過deployment或是relicaset等方式封裝pod運作的細節，如份數等等資訊
* 最後透過service或是ingress連結對外的開放端口提供服務

目前公有雲上有像Google Container Engine(GKE)為您代管K8S集群的服務，以管理整個K8S cluster的角色，透過GKE底層的instance group的操控，進一步做到底層資源的橫向擴展的可能性。