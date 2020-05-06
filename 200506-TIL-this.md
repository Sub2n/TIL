## TIL - this

컴포넌트 클래스의 메소드를 pipe 의 tap 함수의 인자로 넘겼다가 뻘짓을 했다.

자바스크립트의 this는 호출하는 방식에 따라서 다르게 바인딩된다는 걸 알고 있었는데도 this.post = post; 할당이 되지 않는 이유를 한참동안 못 찾았다.

```

@Component({
  selector: 'app-detail',
  templateUrl: './detail.component.html',
  styleUrls: ['./detail.component.scss']
})
export class DetailComponent implements OnInit, OnDestroy {
  public post: Post;
  private id: string;
  private getPost$ = new Subject();
  private destroyed$ = new Subject();

  constructor(
    private postService: PostService,
    private route: ActivatedRoute,
    private router: Router
  ) {
  }

  public ngOnInit(): void {
    this.id = this.route.snapshot.paramMap.get('id');
    forkJoin([
      this.getPost$
        .pipe(
          exhaustMap((id: string) => this.postService.getPost(id)),
          tap((post) => this.initialize(post)) // arrow function 안쓰고 메소드 쓰면 this가 이상하게 바인딩 됨!
          // tap(this.initialize) 로 쓰면 메소드의 특성 상 (instance객체).(this.initialize)에서 this가 instance 객체가 된다.
        )
    ])
      .pipe(
        takeUntil(this.destroyed$)
      )
      .subscribe();
    this.getPost(this.id);
  }

  public ngOnDestroy(): void {
    this.destroyed$.next();
  }

  private getPost(id: string) {
    this.getPost$.next(id);
  }

  private initialize(post: Post) {
    this.post = post;
    this.comments = this.post.comments.map(comment => ({
      ...comment,
      time: (comment.time as Timestamp).toDate(),
      edit: false,
      input: new FormControl(comment.content)
    })).sort(toLatest);
    this.nameInput = new FormControl(this.post.name);
    this.contentInput = new FormControl(this.post.content);
    this.commentInput = new FormControl('');
    this.editorModel = {
      editorData: `<p>${this.post.content}</p>`
    };
  }
}
```

ES6 문법을 쓰면서 항상 쓰는 이유를 생각해야겠다!
