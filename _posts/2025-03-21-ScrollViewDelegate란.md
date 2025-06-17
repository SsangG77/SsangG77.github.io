---
title: ScrollViewDelegate란
date: 2025-03-21 12:00:00 +0900
categories: [개념, Swift]
tags: [ScrollView]
---


iOS 앱 개발에서 스크롤 인터랙션은 사용자 경험의 핵심 요소입니다. UIScrollViewDelegate는 스크롤 뷰의 다양한 동작을 감지하고 제어할 수 있게 해주는 강력한 프로토콜입니다. 이 글에서는 ScrollViewDelegate의 핵심 개념부터 실제 구현까지 상세하게 다루어보겠습니다.

# ScrollViewDelegate란?
UIScrollViewDelegate는 UIScrollView 클래스의 delegate 프로토콜로, 스크롤 이벤트를 처리하고 스크롤 동작을 커스터마이징할 수 있게 해줍니다. UITableView, UICollectionView, UIScrollView 등 모든 스크롤 가능한 뷰에서 사용할 수 있습니다.

```swift
swiftclass ViewController: UIViewController, UIScrollViewDelegate {
    @IBOutlet weak var scrollView: UIScrollView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        scrollView.delegate = self
    }
}
```

<br>

# 주요 Delegate 메서드들

## 1. 스크롤 감지 메서드
`scrollViewDidScroll(_:)`  
스크롤이 발생할 때마다 호출되는 가장 빈번히 사용되는 메서드입니다.

```swift

swiftfunc scrollViewDidScroll(_ scrollView: UIScrollView) {
    let offsetY = scrollView.contentOffset.y
    print("현재 스크롤 위치: \(offsetY)")
    
    // 스크롤 진행률 계산
    let maxOffsetY = scrollView.contentSize.height - scrollView.frame.height
    let progress = offsetY / maxOffsetY
    
    // 헤더 알파값 조정 예시
    headerView.alpha = min(1.0, progress * 2)
}

```

`scrollViewWillBeginDragging(_:)`  
사용자가 스크롤을 시작하려고 할 때 호출됩니다.

```swift
swiftfunc scrollViewWillBeginDragging(_ scrollView: UIScrollView) {
    print("스크롤 시작 준비")
    // 스크롤 시작 시 애니메이션 중단
    UIView.animate(withDuration: 0.3) {
        self.navigationBar.alpha = 0.7
    }
}

```

`scrollViewDidEndDragging(_:willDecelerate:)`
사용자가 드래그를 종료했을 때 호출됩니다.


```swift

swiftfunc scrollViewDidEndDragging(_ scrollView: UIScrollView, willDecelerate decelerate: Bool) {
    if !decelerate {
        // 감속 없이 바로 정지하는 경우
        handleScrollEnd()
    }
}
```

<br>

## 2. 줌 관련 메서드
`viewForZooming(in:)`
줌 가능한 뷰를 반환합니다.


```swift
swiftfunc viewForZooming(in scrollView: UIScrollView) -> UIView? {
    return imageView
}

func scrollViewDidZoom(_ scrollView: UIScrollView) {
    // 줌 후 이미지 중앙 정렬
    centerImageView()
}
```

<br>

## 3. 페이징 관련 메서드
`scrollViewDidEndDecelerating(_:)`  

스크롤 감속이 완료되었을 때 호출됩니다.  

```swift
swiftfunc scrollViewDidEndDecelerating(_ scrollView: UIScrollView) {
    let pageWidth = scrollView.frame.width
    let currentPage = Int(scrollView.contentOffset.x / pageWidth)
    pageControl.currentPage = currentPage
}
```

<br>  

##실제 구현 예시


### 1. 무한 스크롤 구현  

```swift
swiftclass InfiniteScrollViewController: UIViewController {
    @IBOutlet weak var tableView: UITableView!
    private var data: [String] = []
    private var isLoading = false
    
    override func viewDidLoad() {
        super.viewDidLoad()
        tableView.delegate = self
        loadInitialData()
    }
}

extension InfiniteScrollViewController: UIScrollViewDelegate {
    func scrollViewDidScroll(_ scrollView: UIScrollView) {
        let offsetY = scrollView.contentOffset.y
        let contentHeight = scrollView.contentSize.height
        let frameHeight = scrollView.frame.height
        
        // 하단 100px 전에 도달하면 추가 데이터 로드
        if offsetY > contentHeight - frameHeight - 100 && !isLoading {
            loadMoreData()
        }
    }
    
    private func loadMoreData() {
        guard !isLoading else { return }
        isLoading = true
        
        // API 호출 시뮬레이션
        DispatchQueue.main.asyncAfter(deadline: .now() + 1.0) {
            // 새 데이터 추가
            let newData = self.generateMoreData()
            self.data.append(contentsOf: newData)
            self.tableView.reloadData()
            self.isLoading = false
        }
    }
}

```


### 2. 패럴랙스 효과 구현

```swift
swiftclass ParallaxViewController: UIViewController {
    @IBOutlet weak var scrollView: UIScrollView!
    @IBOutlet weak var backgroundImageView: UIImageView!
    @IBOutlet weak var contentView: UIView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        scrollView.delegate = self
    }
}

extension ParallaxViewController: UIScrollViewDelegate {
    func scrollViewDidScroll(_ scrollView: UIScrollView) {
        let offsetY = scrollView.contentOffset.y
        
        // 패럴랙스 효과: 배경 이미지를 스크롤보다 느리게 이동
        let parallaxOffset = offsetY * 0.5
        backgroundImageView.transform = CGAffineTransform(translationX: 0, y: parallaxOffset)
        
        // 스크롤 방향에 따른 추가 효과
        if offsetY > 0 {
            // 위로 스크롤할 때 배경 이미지 확대
            let scale = 1 + (offsetY / 1000)
            backgroundImageView.transform = backgroundImageView.transform.scaledBy(x: scale, y: scale)
        }
    }
}

```


### 3. 스크롤 방향 감지   

```swift

swiftclass ScrollDirectionViewController: UIViewController {
    @IBOutlet weak var scrollView: UIScrollView!
    private var lastContentOffset: CGFloat = 0
    
    override func viewDidLoad() {
        super.viewDidLoad()
        scrollView.delegate = self
    }
}

extension ScrollDirectionViewController: UIScrollViewDelegate {
    func scrollViewDidScroll(_ scrollView: UIScrollView) {
        let currentOffset = scrollView.contentOffset.y
        let direction: ScrollDirection = currentOffset > lastContentOffset ? .up : .down
        
        handleScrollDirection(direction)
        lastContentOffset = currentOffset
    }
    
    private func handleScrollDirection(_ direction: ScrollDirection) {
        switch direction {
        case .up:
            // 위로 스크롤: 네비게이션 바 숨기기
            hideNavigationBar()
        case .down:
            // 아래로 스크롤: 네비게이션 바 보이기
            showNavigationBar()
        }
    }
}

enum ScrollDirection {
    case up, down
}

```

<br>

## 성능 최적화 팁

### 1. scrollViewDidScroll 최적화  

```swift
swiftfunc scrollViewDidScroll(_ scrollView: UIScrollView) {
    // 연산 횟수 줄이기
    let offsetY = scrollView.contentOffset.y
    
    // 특정 범위에서만 처리
    guard offsetY > 0 && offsetY < scrollView.contentSize.height else { return }
    
    // 디바운싱을 통한 과도한 호출 방지
    NSObject.cancelPreviousPerformRequests(withTarget: self)
    perform(#selector(handleScrollEnd), with: nil, afterDelay: 0.1)
}

@objc private func handleScrollEnd() {
    // 실제 처리 로직
}
```


### 2. 메모리 효율적인 이미지 처리  

```swift
swiftfunc scrollViewDidScroll(_ scrollView: UIScrollView) {
    // 화면에 보이는 이미지만 로드
    let visibleRect = CGRect(origin: scrollView.contentOffset, size: scrollView.bounds.size)
    
    for (index, imageView) in imageViews.enumerated() {
        if visibleRect.intersects(imageView.frame) {
            loadImageIfNeeded(at: index)
        } else {
            // 화면 밖의 이미지는 메모리에서 해제
            imageView.image = nil
        }
    }
}
```

<br>

## 자주 발생하는 문제와 해결책


### 1. 스크롤 충돌 문제

```swift
// 중첩된 스크롤뷰에서 제스처 충돌 해결
func gestureRecognizer(_ gestureRecognizer: UIGestureRecognizer, 
                      shouldRecognizeSimultaneouslyWith otherGestureRecognizer: UIGestureRecognizer) -> Bool {
    return true
}
```


### 2. 스크롤 성능 개선  

```swift
// 스크롤 중 불필요한 애니메이션 비활성화
func scrollViewWillBeginDragging(_ scrollView: UIScrollView) {
    UIView.setAnimationsEnabled(false)
}

func scrollViewDidEndDecelerating(_ scrollView: UIScrollView) {
    UIView.setAnimationsEnabled(true)
}
```

<br> 


# 결론
ScrollViewDelegate는 iOS 앱의 스크롤 인터랙션을 풍부하게 만들어주는 핵심 도구입니다. 기본적인 스크롤 감지부터 복잡한 패럴랙스 효과까지, 다양한 사용자 경험을 구현할 수 있습니다.








