

## 📱 Screenshots - iPhone

<div align='center'>
  <img src="https://github.com/user-attachments/assets/48a33d53-9f76-4463-a5ee-106bba17dfeb" width="240">
</div>


## Code

```

private lazy var animatedViews: [(UIView, UIView.TargetPosition)] = [
  (createView(color: .systemBlue), .leftBottom),
  (createView(color: .systemRed), .rightBottom),
  (createView(color: .systemGreen), .rightTop),
  (createView(color: .systemYellow), .leftTop)
]

```

```

private func createView(color: UIColor) -> UIView {
  let view = UIView()
  view.backgroundColor = color
  view.frame = CGRect(
    x: 20,
    y: UIScreen.main.bounds.height - 100,
    width: 50,
    height: 50)
  view.layer.cornerRadius = 25
  return view
}

```

- Scalable and path-based animation
 
```

animatedViews.forEach { view, position in
  self.view.addSubview(view)
  view.animate(to: position.nextPosition)
  view.animateAlongPath(to: position.nextPosition)
}

```
- OR

```

animatedViews.forEach { view, position in
  self.view.addSubview(view)
  view.animate(to: position.nextPosition)
  view.animateAlongPath(to: position.nextPosition)
}

```


- Extension code

```
extension UIView {
    enum TargetPosition {
        case leftTop, leftBottom, rightTop, rightBottom
        
        var nextPosition: TargetPosition {
            switch self {
            case .leftTop: return .rightTop
            case .rightTop: return .rightBottom
            case .rightBottom: return .leftBottom
            case .leftBottom: return .leftTop
            }
        }
        
        func targetOrigin(view: UIView) -> CGPoint {
            switch self {
            case .leftTop:
                return CGPoint(
                    x: 20,
                    y: 50)
            case .leftBottom:
                return CGPoint(
                    x: 20,
                    y: UIScreen.main.bounds.height - view.frame.height - 20)
            case .rightTop:
                return CGPoint(
                    x: UIScreen.main.bounds.width - view.frame.width - 20,
                    y: 50)
            case .rightBottom:
                return CGPoint(
                    x: UIScreen.main.bounds.width - view.frame.width - 20,
                    y: UIScreen.main.bounds.height - view.frame.height - 20)
            }
        }
        
        func targetPoint(for view: UIView) -> CGPoint {
            switch self {
            case .leftTop:
                return CGPoint(x: 20 + view.frame.width / 2, y: 50 + view.frame.height / 2)
            case .leftBottom:
                return CGPoint(x: 20 + view.frame.width / 2, y: UIScreen.main.bounds.height - view.frame.height / 2 - 20)
            case .rightTop:
                return CGPoint(x: UIScreen.main.bounds.width - view.frame.width / 2 - 20, y: 50 + view.frame.height / 2)
            case .rightBottom:
                return CGPoint(x: UIScreen.main.bounds.width - view.frame.width / 2 - 20, y: UIScreen.main.bounds.height - view.frame.height / 2 - 20)
            }
        }
        
    }
    
    func animate(
        to position: TargetPosition,
        duration: TimeInterval = 2.0,
        delay: TimeInterval = 0,
        options: UIView.AnimationOptions = [.curveEaseInOut]
    ) {
        let targetOrigin = position.targetOrigin(view: self)
        UIView.animate(withDuration: duration, delay: delay, options: options) {
            self.frame.origin = targetOrigin
        }
    }
    
    func animateAlongPath(
        to position: TargetPosition,
        duration: TimeInterval = 2.0,
        delay: TimeInterval = 0
    ) {
        
        // Initilize
        let path = UIBezierPath()
        path.move(to: self.center)
        path.addLine(to: position.targetPoint(for: self))
        
        // Create keyframe animation
        let animation = CAKeyframeAnimation(keyPath: "position")
        animation.path = path.cgPath
        animation.duration = duration
        animation.beginTime = CACurrentMediaTime() + delay
        animation.timingFunction = CAMediaTimingFunction(name: .easeInEaseOut)
        
        // Apply the animation
        self.center = position.targetPoint(for: self)
        self.layer.add(animation, forKey: "moveAlongPath")
    }
}


```
