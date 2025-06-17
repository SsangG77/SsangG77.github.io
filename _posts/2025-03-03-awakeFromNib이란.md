---
title: awakeFromNib이란
date: 2025-03-03 12:00:00 +0900
categories: [개념, Swift]
tags: []
---

# awakeFromNib 완벽 가이드: Interface Builder 객체 초기화의 모든 것

## 소개

iOS 개발에서 Interface Builder(XIB/Storyboard)를 사용할 때, 객체의 초기화 시점을 정확히 파악하는 것은 매우 중요합니다. `awakeFromNib`은 XIB나 Storyboard에서 로드된 객체가 완전히 초기화된 후 호출되는 핵심 메서드입니다. 이 글에서는 `awakeFromNib`의 동작 원리부터 실제 활용법까지 상세히 알아보겠습니다.

## awakeFromNib이란?

`awakeFromNib`은 NSObject의 메서드로, Interface Builder 파일(.xib 또는 Storyboard)에서 객체가 언아카이브(unarchive)되고 모든 연결이 완료된 후 자동으로 호출됩니다.

### 기본 개념

```swift
class CustomViewController: UIViewController {
    @IBOutlet weak var titleLabel: UILabel!
    @IBOutlet weak var subtitleLabel: UILabel!
    
    override func awakeFromNib() {
        super.awakeFromNib()
        print("awakeFromNib 호출됨")
        setupInitialConfiguration()
    }
    
    private func setupInitialConfiguration() {
        // 이 시점에서 모든 IBOutlet이 연결되어 있음
        titleLabel.font = UIFont.boldSystemFont(ofSize: 18)
        subtitleLabel.textColor = .systemGray
    }
}
```

## awakeFromNib의 특징

### 1. 호출 조건

```swift
// ✅ awakeFromNib 호출됨 - XIB/Storyboard에서 로드
let viewController = storyboard?.instantiateViewController(withIdentifier: "CustomVC") as! CustomViewController

// ❌ awakeFromNib 호출되지 않음 - 코드로 직접 생성
let viewController = CustomViewController()
```

### 2. 호출 시점

```swift
class LifecycleViewController: UIViewController {
    override func awakeFromNib() {
        super.awakeFromNib()
        print("1. awakeFromNib")
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        print("2. viewDidLoad")
    }
    
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        print("3. viewWillAppear")
    }
    
    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        print("4. viewDidAppear")
    }
}
```

**호출 순서**: `awakeFromNib` → `viewDidLoad` → `viewWillAppear` → `viewDidAppear`

### 3. IBOutlet 연결 상태

```swift
class OutletTestViewController: UIViewController {
    @IBOutlet weak var testLabel: UILabel!
    
    override func awakeFromNib() {
        super.awakeFromNib()
        // ✅ 안전하게 IBOutlet 사용 가능
        testLabel.text = "awakeFromNib에서 설정"
        print("IBOutlet 연결 상태: \(testLabel != nil)") // true
    }
}
```

## 실제 활용 예시

### 1. 커스텀 뷰 초기화

```swift
@IBDesignable
class CardView: UIView {
    @IBOutlet weak var titleLabel: UILabel!
    @IBOutlet weak var contentLabel: UILabel!
    @IBOutlet weak var iconImageView: UIImageView!
    
    override func awakeFromNib() {
        super.awakeFromNib()
        setupCardAppearance()
    }
    
    private func setupCardAppearance() {
        // 그림자 설정
        layer.shadowColor = UIColor.black.cgColor
        layer.shadowOpacity = 0.1
        layer.shadowOffset = CGSize(width: 0, height: 2)
        layer.shadowRadius = 4
        
        // 모서리 둥글게
        layer.cornerRadius = 12
        
        // 배경색 설정
        backgroundColor = .systemBackground
        
        // 레이블 스타일링
        titleLabel.font = UIFont.boldSystemFont(ofSize: 16)
        contentLabel.font = UIFont.systemFont(ofSize: 14)
        contentLabel.textColor = .systemGray
        
        // 아이콘 설정
        iconImageView.tintColor = .systemBlue
        iconImageView.contentMode = .scaleAspectFit
    }
}
```

### 2. 테이블뷰 셀 초기화

```swift
class UserTableViewCell: UITableViewCell {
    @IBOutlet weak var profileImageView: UIImageView!
    @IBOutlet weak var nameLabel: UILabel!
    @IBOutlet weak var emailLabel: UILabel!
    @IBOutlet weak var statusView: UIView!
    
    override func awakeFromNib() {
        super.awakeFromNib()
        setupCellAppearance()
    }
    
    private func setupCellAppearance() {
        // 프로필 이미지 원형으로 만들기
        profileImageView.layer.cornerRadius = 25
        profileImageView.clipsToBounds = true
        profileImageView.layer.borderWidth = 2
        profileImageView.layer.borderColor = UIColor.systemGray5.cgColor
        
        // 이름 레이블 스타일링
        nameLabel.font = UIFont.boldSystemFont(ofSize: 16)
        nameLabel.textColor = .label
        
        // 이메일 레이블 스타일링
        emailLabel.font = UIFont.systemFont(ofSize: 14)
        emailLabel.textColor = .systemGray
        
        // 상태 표시 뷰
        statusView.layer.cornerRadius = 6
        statusView.backgroundColor = .systemGreen
        
        // 셀 스타일링
        selectionStyle = .none
        backgroundColor = .systemBackground
    }
    
    func configure(with user: User) {
        nameLabel.text = user.name
        emailLabel.text = user.email
        profileImageView.image = user.profileImage
        statusView.backgroundColor = user.isOnline ? .systemGreen : .systemGray
    }
}
```

### 3. 컬렉션뷰 셀 초기화

```swift
class PhotoCollectionViewCell: UICollectionViewCell {
    @IBOutlet weak var imageView: UIImageView!
    @IBOutlet weak var titleLabel: UILabel!
    @IBOutlet weak var overlayView: UIView!
    
    override func awakeFromNib() {
        super.awakeFromNib()
        setupCellLayout()
    }
    
    private func setupCellLayout() {
        // 셀 모서리 둥글게
        layer.cornerRadius = 8
        clipsToBounds = true
        
        // 이미지뷰 설정
        imageView.contentMode = .scaleAspectFill
        imageView.clipsToBounds = true
        
        // 오버레이 그라데이션 설정
        setupGradientOverlay()
        
        // 타이틀 레이블 설정
        titleLabel.font = UIFont.boldSystemFont(ofSize: 14)
        titleLabel.textColor = .white
        titleLabel.numberOfLines = 2
    }
    
    private func setupGradientOverlay() {
        let gradientLayer = CAGradientLayer()
        gradientLayer.colors = [
            UIColor.clear.cgColor,
            UIColor.black.withAlphaComponent(0.7).cgColor
        ]
        gradientLayer.locations = [0.0, 1.0]
        overlayView.layer.addSublayer(gradientLayer)
        
        // 레이아웃 업데이트 시 그라데이션 크기 조정
        DispatchQueue.main.async {
            gradientLayer.frame = self.overlayView.bounds
        }
    }
}
```

## 주의사항과 모범 사례

### 1. super.awakeFromNib() 호출

```swift
// ✅ 올바른 사용
override func awakeFromNib() {
    super.awakeFromNib() // 반드시 먼저 호출
    setupUI()
}

// ❌ 잘못된 사용
override func awakeFromNib() {
    setupUI()
    // super.awakeFromNib() 호출 누락 - 예상치 못한 동작 발생 가능
}
```

### 2. 프레임 정보 사용 시 주의

```swift
class FrameAwareView: UIView {
    @IBOutlet weak var circleView: UIView!
    
    override func awakeFromNib() {
        super.awakeFromNib()
        // ⚠️ 이 시점에서 프레임이 최종 크기가 아닐 수 있음
        setupInitialAppearance()
    }
    
    override func layoutSubviews() {
        super.layoutSubviews()
        // ✅ 정확한 프레임 정보 사용 가능
        circleView.layer.cornerRadius = circleView.frame.width / 2
    }
    
    private func setupInitialAppearance() {
        // 프레임에 의존하지 않는 설정만 수행
        circleView.backgroundColor = .systemBlue
        circleView.clipsToBounds = true
    }
}
```

### 3. 메모리 관리

```swift
class MemoryAwareViewController: UIViewController {
    @IBOutlet weak var scrollView: UIScrollView!
    private var observer: NSObjectProtocol?
    
    override func awakeFromNib() {
        super.awakeFromNib()
        setupObservers()
    }
    
    private func setupObservers() {
        observer = NotificationCenter.default.addObserver(
            forName: UIApplication.didReceiveMemoryWarningNotification,
            object: nil,
            queue: .main
        ) { [weak self] _ in
            self?.handleMemoryWarning()
        }
    }
    
    private func handleMemoryWarning() {
        // 메모리 정리 로직
    }
    
    deinit {
        if let observer = observer {
            NotificationCenter.default.removeObserver(observer)
        }
    }
}
```

## 성능 최적화 팁

### 1. 지연 초기화 활용

```swift
class OptimizedViewController: UIViewController {
    @IBOutlet weak var heavyContentView: UIView!
    private var isHeavyContentInitialized = false
    
    override func awakeFromNib() {
        super.awakeFromNib()
        // 기본적인 설정만 수행
        setupBasicAppearance()
    }
    
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        // 실제 표시되기 전에 무거운 작업 수행
        initializeHeavyContentIfNeeded()
    }
    
    private func setupBasicAppearance() {
        view.backgroundColor = .systemBackground
    }
    
    private func initializeHeavyContentIfNeeded() {
        guard !isHeavyContentInitialized else { return }
        
        // 복잡한 UI 설정이나 데이터 로딩
        setupComplexAnimations()
        loadInitialData()
        
        isHeavyContentInitialized = true
    }
}
```

### 2. 조건부 초기화

```swift
class ConditionalViewController: UIViewController {
    @IBOutlet weak var premiumFeatureView: UIView!
    @IBOutlet weak var standardView: UIView!
    
    override func awakeFromNib() {
        super.awakeFromNib()
        setupUIBasedOnUserType()
    }
    
    private func setupUIBasedOnUserType() {
        let isPremiumUser = UserManager.shared.isPremiumUser
        
        if isPremiumUser {
            setupPremiumFeatures()
        } else {
            setupStandardFeatures()
        }
    }
    
    private func setupPremiumFeatures() {
        premiumFeatureView.isHidden = false
        standardView.isHidden = true
        // 프리미엄 기능 초기화
    }
    
    private func setupStandardFeatures() {
        premiumFeatureView.isHidden = true
        standardView.isHidden = false
        // 표준 기능 초기화
    }
}
```


## 결론

`awakeFromNib`은 Interface Builder로 생성된 객체의 초기화에 있어서 핵심적인 역할을 합니다. IBOutlet이 모두 연결된 후 호출되므로 UI 초기 설정에 이상적이며, 올바른 사용법을 익히면 더 안정적이고 유지보수가 쉬운 코드를 작성할 수 있습니다.

**핵심 포인트:**
- XIB/Storyboard에서 로드된 객체에서만 호출
- IBOutlet이 모두 연결된 후 실행
- viewDidLoad보다 먼저 호출
- UI 초기 설정에 최적화된 시점
- 성능과 메모리 관리를 고려한 구현 필요

`awakeFromNib`을 제대로 활용하여 더 나은 iOS 앱을 개발해보세요!

## 추가 학습 자료

- Apple Developer Documentation: awakeFromNib
- iOS App Programming Guide: View Controller Lifecycle
- Interface Builder User Guide

---


