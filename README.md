# Trakt OpenAPI Specification

Part of Trakt API specification in OpenAPI YAML format.

## Generate Dart (Flutter) client code

Run
```bash
sh ./build.sh
```
or
```bash
mvn clean install
```
and then
```bash
sh ./generate.sh
```

## Use Dart (Flutter) client code

Add dependency to pubspec.yaml:
```yaml
trakt_api:  
  path: ../trakt-openapi/target/generated-sources/trakt_api
```
run:
```bash
flutter pub get
```
use in code:
```dart
import 'package:trakt_api/trakt_api.dart' as trakt;

final trakt.TraktApi traktApi;

// set up api key and authentication
traktApi.dio.interceptors.add(
  InterceptorsWrapper(
    onRequest: (RequestOptions options, RequestInterceptorHandler handler) async {
      final token = await tokenStorage.read();
      final accessToken = token?.accessToken;

      options.headers['trakt-api-key'] = clinetId;
      options.headers['trakt-api-version'] = '2';
      if (accessToken != null) {
        options.headers['Authorization'] = 'Bearer $accessToken';
      }

      return handler.next(options);
    },
    onError: (e, handler) async {
      if (e.response?.statusCode == 401) {
        // refresh auth token/handle auth errors
      }

      return handler.next(e);
    },
  ),
);

// call api
final showsApi = traktApi.getShowsApi();
final watchProgressResponse = await showsApi.getShowWatchedProgress(id: 1));
```