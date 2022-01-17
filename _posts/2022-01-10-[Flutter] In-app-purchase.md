---
title: "[Flutter]In-App-Purchase"
categories: flutter
toc: true
toc_label: "목차"
toc_icon: "cog"
---

### 오늘은 여담 먼저...

처음 in_app_purchase 를 구현 할 때, 프로젝트의 플러터 버전이 2.x 버전이 아니라, null safety가 적용 안된 0.35 버전으로 적용했다. 그러다 보니 옛날 라이브러리라 원하는 내용과 코드가 안나와 애를 먹었다. 특히 ios에서만 결과가 purchased가 안오고 계속 pending이 나오는 문제가 있었다. 2주동안 끙끙 거렸는데, 역시 답은 쉬운곳에...

[https://github.com/flutter/flutter/issues/68626#issuecomment-716695076](https://github.com/flutter/flutter/issues/68626#issuecomment-716695076)

ios 샌드박스 결제를 할 때, IsSendBoxTesting을 당연히 true로 해야하는 줄 알았는데, 이것 때문에 오류가 계속 났던 것. false로 하니 pruchased 응답이 너무 잘 온다 ㅡㅡ 화난다.

아무튼 이렇게 개발을 해놨는데, 11월 부터 구글결제 라이브러리 3.0 버전이 적용되지 않으면 쓸 수 없다는 메세지(?)를 받았다. 0.35 버전은 빌링 라이브러리 3.0이 적용되지 않았기 때문에... flutter upgrade를 하고 다시 인앱결제를 구현하기로 했다.

## 인앱결제 구현하기

대부분의 코드는 in_app_purchase 에서 제공하고 있고, 나는 이 코드를 view와 getxController로 분리하는 작업을 진행했다. 이 과정에서 처음 보는 사람이라면 헷갈리거나 놓칠거 같다고 생각하는 부분을 살펴보자. 참고로 여기선 소비성 상품을 사는 경우만 고려했다. 

### 초기화

```dart
late StreamSubscription<List<PurchaseDetails>> _subscription;

@override
  void onInit() {
    super.onInit();
    final Stream<List<PurchaseDetails>> purchaseUpdated = InAppPurchase.instance.purchaseStream;

    _subscription = purchaseUpdated.listen(
      (purchaseDetailsList) {
        _listenToPurchaseUpdated(purchaseDetailsList);
      },
      onDone: () {
        _subscription.cancel();
      },
      onError: (error) {
        debugPrint('$error');
      },
    );
    _initStoreInfo();
  }

@override
  void onClose() {
    _subscription.cancel();
    super.onClose();
  }
```

우선 subscription을 init 하고 dispose 하는 작업이 필요하다.

### 인앱결제 사용유무 판단

```dart
Future<void> _initStoreInfo() async {
		//인앱결제를 할 수 있는지 확인
    final bool isAvailable = await _inAppPurchase.isAvailable();
    if (!isAvailable) {
        _purchases = [];
        purchasePending.value = false;
      return;
    }
		
    final ProductDetailsResponse productDetailResponse =
        await _inAppPurchase.queryProductDetails(_kProductIds.toSet());
    if (productDetailResponse.error != null) {
      _queryProductError = productDetailResponse.error!.message;
      _purchases = [];
      purchasePending.value = false;
      Fluttertoast.showToast(msg: '$_queryProductError');
      return;
    }

    if (productDetailResponse.productDetails.isEmpty) {
        _queryProductError = null;
        _purchases = [];
        purchasePending.value = false;
      return;
    }
  }
```

인앱결제를 사용할 수 있는지 확인하고, 

### 판매상품이 목록에 있는지 확인

```dart
Future<void> _buyProduct(ProductDetails productDetails, {bool consumable = true}) async {
    if (!await checkSalesList(productDetails)) return; // 판매상품 목록에 판매하는 상품이 있는지 확인
    final PurchaseParam purchaseParam = _getPurchaseParam(productDetails);

    if (consumable) {
      buyConsumable(purchaseParam);
    } else {
      buyNonConsumable(purchaseParam);
    } 
  }
```

상품이 구글플레이스토어와 앱스토어에 등록되어 있는지 확인하는 절차다. 구글플레이스토어와 인앱스토어에서 상품을 등록해야 상품 정보를 불러와 구매하려는 상품정보가 맞는지 확인한다.

### 소모성 상품 처리하기

그 다음 소모성 상품이냐 아니냐에 따라 분기 처리~!

```dart
void buyConsumable(PurchaseParam purchaseParam) {
     _inAppPurchase.buyConsumable(
        purchaseParam: purchaseParam,
        autoConsume: _kAutoConsume || Platform.isIOS,
     );
  }
```

이렇게 하면 이제 각 스토어로 구매 신호를 보낸다. 그러면 init에서 구현했던 subscription이 응답이 오길 listen 하고 있다가 _listenToPurchaseUpdated 함수를 통해서 각 응답에 맞는 처리를 한다.

### subscriptiond으로 응답값 처리하기

_listenToPurchaseUpdated 함수도 사이트에 나와 있는데로 하면 되는데, 유의해야할 점이 한 부분 있다.

```dart
void _listenToPurchaseUpdated(List<PurchaseDetails> purchaseDetailsList) {
    purchaseDetailsList.forEach((PurchaseDetails purchaseDetails) async {

        switch (purchaseDetails.status) {
          case PurchaseStatus.pending:
            _showPendingUI();
            break;

          case PurchaseStatus.error:
            _errorPurchase(purchaseDetails);
            await _completePurchase(purchaseDetails);

            break;

          case PurchaseStatus.canceled:
            await _cancelPurchase(purchaseDetails);
            break;

          case PurchaseStatus.restored:
          case PurchaseStatus.purchased:
            final bool valid = await _verifyPurchase(purchaseDetails);
            if (valid) {
              _deliverProduct(purchaseDetails);
            } else {
              _handleInvalidPurchase(purchaseDetails);
              return;
            }
            await _completePurchase(purchaseDetails);
        
            break;
          default:
            break;
        }

    });
  }

Future<void> _completePurchase(PurchaseDetails purchaseDetails) async {
    _unShownPendingUI();
    if (purchaseDetails.pendingCompletePurchase) {
      bool result = false;
      **await _inAppPurchase.completePurchase(purchaseDetails);**
    }
  }
```

바로 어떤 purchaseStatus를 받게 되더라도 completePurchase를 찍어줘야 한다는 것. (Pending 경우 제외) 이렇게 안하면, 오류나거나 구매한 후 바로 상품을 눌렀을 때, 이미 대기열에 있는 상품이라고 뜨며 오류가 발생한다.

큰 틀은 in_app_purchase example에서 주는 형식과 크게 다르지 않다. 이를 조합해 내가 제공하는 서비스에 맞게 구현하는건 역시 또 다른 문제다😅

인앱결제를 출시한지 한달이 되는 시점에서 아직까지 인앱결제에 대한 오류문의는 들어오지 않아 참 다행이라고 생각한다. (결제에 관한 오류 민원은 진짜 질리도록 겪어봐서 다시는 겪고 싶지 않다...)