### DB서버 실행
- 터미널에서 ```mongod``` 입력

### DB서버에 접속하기
- 새 터미널에서 ```mongo``` 입력

### DB 생성
- ```use mongodb-tutorial``` mongodb-turorial을 db라는 alias로 접근할 수 있게 함.
- ```show dbs``` db 목록에 아직 mongodb-tutorial db가 생성되어있지 않음을 확인하고,
- ```db.member.isert({'name': 'bsscco'});``` db에 document(JSON형태의 객체)를 넣으면 db가 생성됨.
- ```show dbs```를 다시 입력해보면 mongodb-tutorial이 생성되어있는 것을 확인할 수 있다.

### DB 제거
- ```db.dropDatabase()```

### collection 생성
- ```db.createCollection('member')``` 근데 collection을 미리 만들어둘 필요가 없음.
- ```db.member.isert(...``` 위에서 이렇게 document를 넣을 때 member collection이 같이 만들어지니까 이게 더 편리함.
- collection을 미리 만드는 이유는 ```db.createCollection(name, [option])```에서 option을 줄 수 있기 때문

### collection 보기
- ```show collections```

### collection 제거
- ```db.member.drop()```

### document 삽입
- ```db.member.insert({'name': 'bssscco'})```
- 여러 개 삽입 ```db.member.insert([{'name':'one'}, {'name':'two'}])```

### document 제거
- ```db.member.remove({'name': 'bsscco'})```
- 첫번째 인자 criteria에 만족하는 document가 여러 개일 때 하나만 제거하고 싶으면 ```db.member.remove({'name': 'bsscco'}, true)```를 실행한다. 두번 째 인자 justOne은 기본값이 false이다.

### document 조건으로 찾기
- ```db.member.find()```는 member collection의 모든 document를 찾아준다.
- ```db.member.find().pretty()```는 document 하나하나가 값이 많아서 보기 안 좋을 때 document마다 multi-line으로 보여준다.
- query를 주려면 ```db.member.find({'name': 'one'})```으로 입력한다.

- query에 비교 연산자 사용하기 ```db.number.find({'value', {$gt: 0, $lt:100, $nin: [12, 33]}})```
- query 비교 연산자
	- $eq : 같다.
	- $neq : 같지 않다.
	- $gt : 크다.
	- $gte : 크거나 같다.
	- $lt : 작다.
	- $lte : 작거나 같다.
	- $in : 배열 속에 있다.
	- #nin : 배열 속에 없다.

- query에 논리 연산자 사용하기 ```db.post.find({$and: [{'title': 'good title'}, {'order', {$gt: 10}}]})```
- query 논리 연산자
	- $and
	- $or
	- $not : 조건이 false이면 true, true이면 false
	- $nor : 모든 조건이 false일 때 true

- query에 정규표현식 사용하기 ```db.post.find('title': /^bs.+co$/i``` i는 대소문자 무시라는 뜻.
- query 정규표현식 옵션
	- i : 대소문자 무시
	- m : ^를 사용할 때 \n이 있다면 무력화
	- x : 정규식 안에 있는 whitespace를 모두 무시
	- s : .을 사용할 때 \n을 포함해서 매치
	
- query에 javascript표현식 사용하기 ```db.post.find({$where: this.comments.length == 0'})```
- query에서 subdocument에 조건 걸기 ```db.post.find({'comments': {$elemMatch: {'name': 'bsscco'}}})```
	
### document 찾은 결과에서 필요한 element만 뽑기
- projection 하기 ```db.posts.find({}, {'_id':false, 'title':true})```
- projection에서 subdocument에 limit 정하기 ```db.posts.find({}, {'comments': {$slice: 1}})```
- projection에서 subdocument에 조건 걸기 ```db.posts.find({}, {'comments': {$elemMatch: {'name': 'bsscco'}}})```
	
### document 찾은 결과 가공하기
- 정렬하기 ```db.posts.find().sort({name : 1})``` 1은 오름차순, -1은 내림차순
- limit 정하기 ```db.posts.find().sort({name : 1}).limit(3)```
- 앞에서 skip 하기 ```db.posts.find().sort({name : 1}).skip(2)```

### 팁
- mongo db는 javascript 기반이라서 자주 쓰는 쿼리는 함수로 만들어서 사용할 수도 있다.
	- ```var showPage = function(page) {return db.numbers.find().sort({'value':1}).skip((page-1)*5).limit(5)}``` 5개씩 보여준다.

### document 수정
- field 수정하기 ```db.members.update({'name': 'bsscco'}, {$set: {age: 20}})```
- document 대체하기 ```db.members.update({'name': 'bsscco'}, {'name': 'bsscco2', 'age': 21})```
- field 제거하기 ```db.members.update({'name': 'bsscco'}, {$unset: {'age': 1}})``` ```{'age': 1}```에서 1이 있는 자리엔 어떤 값을 넣어도 상관 없음.
- document가 없으면 추가해서 field 수정 ```db.members.update({'name': 'newName'}, {$set: {'name': 'newName', 'age': 10}}, {upsert: true})```

### 배열 field 수정
- 값 추가하기 ```db.member.update({'name': 'bsscco'}, {$push: {'habits': 'baseball'}})```
- 여러 개 값 추가하기 ```db.member.update({'name': 'bsscco'}, {$push: {''habits': {$each: ['baseball', 'basketball']}}})```
- 여러 개 값 추가해서 정렬시키기  ```db.member.update({'name': 'bsscco'}, {$push: {''habits': {$each: ['baseball', 'basketball'], $sort: 1}}})```
- 값 제거하기 ```db.member.update({'name': 'bsscco'}, {$pull: {'habits': 'baseball'}})```
- 여러 개 값 제거하기  ```db.member.update({'name': 'bsscco'}, {$pull: {''habits': {$in: ['baseball', 'basketball']}}})```
