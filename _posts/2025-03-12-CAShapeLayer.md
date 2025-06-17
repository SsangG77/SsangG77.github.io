---
title: CAShapeLayer
date: 2025-03-12 12:00:00 +0900
categories: [개념, UIKit]
tags: [UIKit]
---

`CAShapeLayer`는 벡터 기반의 경로(path)를 그릴 수 있는 `CALayer`의 서브클래스이다.

- Core Graphics (`CGPath`)를 사용하여 선, 도형, 곡선 등을 그릴 수 있음
- 애니메이션을 추가하여 동적으로 변화하는 UI를 만들기 용이
- 비트맵(`UIImageView`)보다 성능이 뛰어나며, GPU를 활용하여 렌더링 성능이 우수

### **`CAShapeLayer`의 주요 속성**

| 속성 | 설명 |
| --- | --- |
| `path` | 그릴 경로(`CGPath`)를 설정 |
| `strokeColor` | 선의 색상 설정 (`CGColor`) |
| `fillColor` | 내부 색상 설정 (`CGColor`, `nil`이면 투명) |
| `lineWidth` | 선의 두께 설정 (`CGFloat`) |
| `lineDashPattern` | 점선 패턴 설정 (`[NSNumber]`) |
| `lineCap` | 선의 끝 모양 설정 (`.butt`, `.round`, `.square`) |
| `lineJoin` | 선이 만나는 모양 설정 (`.miter`, `.round`, `.bevel`) |

## **예제 코드**

### **1. 직선 그리기**

```swift

class LineView: UIView {
    override init(frame: CGRect) {
        super.init(frame: frame)
        drawLine()
    }

    required init?(coder: NSCoder) {
        super.init(coder: coder)
        drawLine()
    }

    private func drawLine() {
        let shapeLayer = CAShapeLayer()
        let path = UIBezierPath()

        path.move(to: CGPoint(x: 20, y: 50))   // 시작점
        path.addLine(to: CGPoint(x: 200, y: 50)) // 끝점

        shapeLayer.path = path.cgPath
        shapeLayer.strokeColor = UIColor.red.cgColor // 선 색상
        shapeLayer.lineWidth = 3.0 // 선 두께

        self.layer.addSublayer(shapeLayer) // 레이어 추가
    }
}

```

**`move(to:)`로 시작점을 지정하고 `addLine(to:)`로 끝점을 지정하여 선을 그림.**

**`strokeColor`와 `lineWidth`로 선의 색상과 두께를 조절.**

---

### **2. 원 그리기 (`UIBezierPath` 활용)**

```swift

class CircleView: UIView {
    override init(frame: CGRect) {
        super.init(frame: frame)
        drawCircle()
    }

    required init?(coder: NSCoder) {
        super.init(coder: coder)
        drawCircle()
    }

    private func drawCircle() {
        let shapeLayer = CAShapeLayer()
        let path = UIBezierPath(ovalIn: CGRect(x: 50, y: 50, width: 100, height: 100))

        shapeLayer.path = path.cgPath
        shapeLayer.strokeColor = UIColor.blue.cgColor
        shapeLayer.fillColor = UIColor.clear.cgColor // 내부 색상을 투명하게 설정
        shapeLayer.lineWidth = 4.0

        self.layer.addSublayer(shapeLayer)
    }
}

```

**원(`oval`)을 `UIBezierPath`로 생성하여 `CAShapeLayer`의 `path`로 설정.**

**`fillColor`를 `clear`로 설정하여 내부를 비우고 선만 표시.**

---

### **3. 선 애니메이션 추가 (`CABasicAnimation`)**

```swift

class AnimatedLineView: UIView {
    override init(frame: CGRect) {
        super.init(frame: frame)
        drawAnimatedLine()
    }

    required init?(coder: NSCoder) {
        super.init(coder: coder)
        drawAnimatedLine()
    }

    private func drawAnimatedLine() {
        let shapeLayer = CAShapeLayer()
        let path = UIBezierPath()

        path.move(to: CGPoint(x: 20, y: 150))
        path.addLine(to: CGPoint(x: 250, y: 150))

        shapeLayer.path = path.cgPath
        shapeLayer.strokeColor = UIColor.orange.cgColor
        shapeLayer.lineWidth = 3.0

        self.layer.addSublayer(shapeLayer)

        // 애니메이션 추가
        let animation = CABasicAnimation(keyPath: "strokeEnd")
        animation.fromValue = 0
        animation.toValue = 1
        animation.duration = 2.0 // 2초 동안 선이 그려짐
        shapeLayer.add(animation, forKey: "lineAnimation")
    }
}

```

**`CABasicAnimation`을 사용하여 `strokeEnd` 값을 변경하면 선이 그려지는 애니메이션을 추가할 수 있음.**

**시작값 `fromValue = 0`, 종료값 `toValue = 1`을 주면 점진적으로 선이 나타남.**
