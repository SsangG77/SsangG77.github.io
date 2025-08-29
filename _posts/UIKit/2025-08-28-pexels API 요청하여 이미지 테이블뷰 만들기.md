---
title: pexels API 요청하여 이미지 테이블뷰 만들기
date: 2025-08-28 12:00:00 +0900
categories: [UIKit, 실습]
tags: [TableView, URLSession, 비동기, image, AutoLayout]
---


# ViewController
```

//MARK: - ViewController
class ViewController: UIViewController {
    
    let viewModel: ViewModel = ViewModel()
    
    let tableView: UITableView = {
        let tableView = UITableView()
        tableView.translatesAutoresizingMaskIntoConstraints = false
        
        tableView.rowHeight = UITableView.automaticDimension
        
        return tableView
    }()
    
    
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .systemGray5
        
        tableView.register(MyCell.self, forCellReuseIdentifier: MyCell.identifier)
        tableView.dataSource = self
        
        
        view.addSubview(tableView)
        
        NSLayoutConstraint.activate([
            tableView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor),
            tableView.leadingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.leadingAnchor),
            tableView.trailingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.trailingAnchor),
            tableView.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor)
        ])
        
        
        // images에 didSet 되면 클로저 호출해서 reload
        viewModel.imagesUpdates = { [weak self] in
            DispatchQueue.main.async {
                self?.tableView.reloadData()
            }
        }
    }
}
```


# ViewController - DataSource
```
//MARK: - DataSource
extension ViewController: UITableViewDataSource {
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return viewModel.images.count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        guard let cell = tableView.dequeueReusableCell(withIdentifier: MyCell.identifier, for: indexPath) as? MyCell else { return MyCell() }
        
        let photo = viewModel.images[indexPath.row]
        let imageLink = photo.src["medium"]
        
        
        let aspectRatio = CGFloat(photo.width) / CGFloat(photo.height)
        cell.configure(url: imageLink, aspectRatio: aspectRatio)
        
        return cell
    }
}
```


# MyCell
```

//MARK: - Cell
class MyCell: UITableViewCell {
    
    static let identifier = "cell"
    
    // 이미지 다운로드 작업 참조 저장
    private var imageDownloadTask: URLSessionDataTask?
    private var aspectRatioConstraint: NSLayoutConstraint?
    
    let _imageView: UIImageView = {
        let imageView = UIImageView()
        imageView.translatesAutoresizingMaskIntoConstraints = false
        imageView.contentMode = .scaleAspectFit
        return imageView
    }()
    
    
    override init(style: UITableViewCell.CellStyle, reuseIdentifier: String?) {
        super.init(style: style, reuseIdentifier: reuseIdentifier)
        
        contentView.addSubview(_imageView)
        
        NSLayoutConstraint.activate([
            _imageView.leadingAnchor.constraint(equalTo: contentView.leadingAnchor),
            _imageView.trailingAnchor.constraint(equalTo: contentView.trailingAnchor),
            _imageView.topAnchor.constraint(equalTo: contentView.topAnchor),
            _imageView.bottomAnchor.constraint(equalTo: contentView.bottomAnchor)
        ])
    }
    
    required init?(coder: NSCoder) {
        fatalError()
    }
    
    override func prepareForReuse() {
            super.prepareForReuse()
            // 셀 재사용 시 이전 다운로드 작업 취소
            imageDownloadTask?.cancel()
            imageDownloadTask = nil
            _imageView.image = nil
        
            if let ratioContraint = aspectRatioConstraint {
                _imageView.removeConstraint(ratioContraint)
                self.aspectRatioConstraint = nil
            }
        }
    
    func configure(url: String?, aspectRatio: CGFloat) {
        setAspectRatio(aspectRatio)
        
        self.imageDownloadTask?.cancel()
        
        guard let urlString = url, let url = URL(string: urlString) else {
            _imageView.image = UIImage(systemName: "square.and.arrow.up")
            return
        }
        
        imageDownloadTask = URLSession.shared.dataTask(with: url) { [weak self] data, response, error in
            
            guard !Task.isCancelled else { return }
            
            guard let self,
                  let data = data,
                  response != nil,
                  error == nil else { return }
            DispatchQueue.main.async {
                self._imageView.image = UIImage(data: data) ?? UIImage(systemName: "square.and.arrow.up")
                
            }
        }
        
        imageDownloadTask?.resume()
    }
    
    private func setAspectRatio(_ ratio: CGFloat) {
        if let aspectRatioContraint = aspectRatioConstraint {
            _imageView.removeConstraint(aspectRatioContraint)
        }
        
        aspectRatioConstraint = _imageView.widthAnchor.constraint(equalTo: _imageView.heightAnchor, multiplier: ratio)
        aspectRatioConstraint?.isActive = true
    }
}

```


# ViewModel
```

//MARK: - ViewModel
class ViewModel {
    var imagesUpdates: (() -> Void)?
    var images: [Photo] = [] {
        didSet {
            self.imagesUpdates?()
        }
    }
    
    init() {
        Task {
            do {
                self.images = try await self.fetchImages()
            } catch {
                print("ViewModel - fetchImages() error")
            }
        }
    }
    
    func fetchImages() async throws -> [Photo] {
        print(#function)
        let apiKey = "ZZBZygLLeWAl779RpQxzPY8J3uGAehcN091dA7KZhFvtOR454tV48vOu"
        let urlString = "https://api.pexels.com/v1/search?query=nature&per_page=10"
        
        guard let url = URL(string: urlString) else {
            print("wrong URL")
            return []
        }
        
        var request = URLRequest(url: url)
        request.httpMethod = "GET"
        request.setValue("application/json", forHTTPHeaderField: "Content-Type")
        // 필요에 따라 추가 헤더 설정
        request.setValue(apiKey, forHTTPHeaderField: "Authorization")

        do {
            let (data, response) = try await URLSession.shared.data(for: request)
            guard (response as? HTTPURLResponse)?.statusCode == 200 else {
                        throw URLError(.badServerResponse) // 서버 응답 오류 처리
                    }
            let result = try JSONDecoder().decode(Response.self, from: data)
            return result.photos
            
        } catch {
            print("Data load Error: \(error)")
            throw error
        }
    }
}
```


#Model
```

//MARK: - Model
struct Response: Decodable {
    var page: Int
    var per_page: Int
    var photos: [Photo]
    var next_page: String
}

struct Photo: Decodable {
    var id: Int
    var width: Int
    var height: Int
    var url: String
    var photographer: String
    var photographer_url: String
    var photographer_id: Int
    var avg_color: String
    var src: [String: String]
    var liked: Bool
    var alt: String
}

```
