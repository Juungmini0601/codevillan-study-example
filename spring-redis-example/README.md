# Redis 모니터링 및 최적화 가이드

## 1. Redis 모니터링 도구 설치

### 1.1 Redis Commander 설치

```bash
# Node.js가 설치되어 있어야 합니다
npm install -g redis-commander

# 실행
redis-commander --redis-host localhost --redis-port 6379
```

## 2. 성능 모니터링

### 2.1 기본 모니터링 명령어

```bash
# Redis CLI 접속
docker exec -it redis-practice redis-cli

# 메모리 사용량 확인
INFO memory

# 클라이언트 연결 상태
INFO clients

# 명령어 통계
INFO commandstats

# 실시간 모니터링
MONITOR
```

### 2.2 Slow Log 설정 및 확인

```bash
# Slow Log 설정 (10ms 이상 소요되는 명령어 기록)
CONFIG SET slowlog-log-slower-than 10000
CONFIG SET slowlog-max-len 128

# Slow Log 조회
SLOWLOG GET
SLOWLOG LEN
SLOWLOG RESET
```

## 3. 성능 최적화

### 3.1 메모리 최적화

```bash
# 메모리 정책 설정
CONFIG SET maxmemory-policy allkeys-lru

# 메모리 한도 설정
CONFIG SET maxmemory 256mb

# 키 만료 설정 확인
SCAN 0 MATCH * TYPE string
```

### 3.2 배치 처리 최적화

```java

@Service
@Slf4j
public class RedisBatchService {
	private final RedisTemplate<String, Object> redisTemplate;

	public void batchProcess(Map<String, Object> dataMap) {
		redisTemplate.executePipelined((RedisCallback<Object>)connection -> {
			dataMap.forEach((key, value) -> {
				byte[] keyBytes = key.getBytes();
				byte[] valueBytes = value.toString().getBytes();
				connection.stringCommands().set(keyBytes, valueBytes);
			});
			return null;
		});
	}
}
```

## 4. 성능 테스트

### 4.1 Redis 벤치마크 도구 사용

```bash
# 기본 벤치마크
redis-benchmark -h localhost -p 6379 -n 100000 -c 50

# 특정 명령어 벤치마크
redis-benchmark -t set,get -n 100000 -q

# 파이프라인 벤치마크
redis-benchmark -P 16 -n 100000 -q
```

## 5. 모니터링 체크리스트

### 5.1 Daily 점검사항

- [ ] 메모리 사용량 확인
- [ ] 연결된 클라이언트 수 확인
- [ ] 초당 처리 명령어 수 확인
- [ ] Slow Log 확인
- [ ] 키 개수 및 만료 설정 확인

### 6.2 Weekly 점검사항

- [ ] 메모리 fragmention 확인
- [ ] 백업 상태 확인
- [ ] 네트워크 지연시간 확인
- [ ] CPU 사용률 확인
- [ ] 디스크 사용량 확인






