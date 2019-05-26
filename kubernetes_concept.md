# Kubernetes概念

開始kubernetes前，有些專有名詞在這邊先介紹一下，讓未來研讀k8s的文件可以更得心應手。(下面文件翻譯自：http://kubernetes.io/docs/user-guide/)

## Cluster
Kubernetes cluster是k8s所使用的一個以上的實體或虛擬主機以及其他的基礎建設(infrastructure)資源的集合，用以提供給k8s使用者執行相關應用程式使用。

## Node
Node是一台執行k8s的實體或虛擬的主機，屬於Cluster的一部份，上面可以執行與排程Pod。

## Pod
Pod是一個Container與Volumes實體存在的一個群組。

## Label
Label是一個key/value組成的記錄，附加在某項資源上面，例如Pod，以傳達使用者所定義的可識別屬性。Label可以用來組織與選擇某個資源的子集合。

## Selector
Selector是一個用來識別相關資源的標籤表示式，例如可以使用selector來查詢哪些pods裡面已經被標註是屬於load-balanced服務的資源。

## Replication Controller
Replication controller (又稱rc) 是用來確保同時間有存在指定數量的複製pod(pod replicas)有在執行。它同時允許使用者很簡單地做複製數量的調整(也稱作scaling)以及操控在機器reboot或是有任何錯誤或關閉時重新創建一個Pod。

## Service
Server是定義一個pods的群組以及如何去存取這些資源的方式，例如該資源的外部IP以及相關DNS的對應名稱等。

## Volume
Volume是一個資料夾型態的資源，可以作為container的一個檔案系統，也可以在其中放置一些資料讓container可以直接存取。Kubernetes volumes是基於docker volumes所建置，並且增加這些volume目錄或裝置創建的功能。

## Secret
Secret是k8s用來儲存敏感資料用的儲存服務，例如認證的authentication token...等等。它可以由containers內部發出請求來取出這些資訊。

## Name
Name(名稱)是用來定義某個資源的識別名稱，通常是由使用者或是k8s client來定義的。識別上Name會是cluster內唯一的識別子，但在該名稱資源移除後，可以再由其他的資源以同樣名稱建立。

## Namespace
Namespace就像某個資源名稱(name)的前綴(prefix)。Namespace可以用來作為不同的專案、組織或是使用者來共享一個cluster的資源，例如用來避免不相干的群組間名稱重複的問題。

## Annotation
Annotation也是一個key/value pair組成的紀錄，相較於label來說，可以乘載更大的數量，也可以是非人類閱讀的資料型態。Annotation的目的是來儲存不可識別的一些輔助資料，尤其是一些由工具或是系統所衍生的資料。但現階段並不支援快速過濾annotation的值。

