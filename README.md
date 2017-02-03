# Spot

[![Build Status](https://circleci.com/gh/kobakei/spot.svg?style=shield)](https://circleci.com/gh/kobakei/spot/tree/master)
[![JitPack](https://jitpack.io/v/kobakei/spot.svg)](https://jitpack.io/#kobakei/spot)
[![Android Arsenal](https://img.shields.io/badge/Android%20Arsenal-Spot-green.svg?style=true)](https://android-arsenal.com/details/1/3374)

**Spot** (**S**hared**P**references **O**bject **T**ransformer) is Android object-data mapper for SharedPreferences.

Main features are as below:

- **No reflection**: All repository classes will be generated by annotation processing.
- **POJO support**: Entity classes do not need to inherit base class.
- **Store any types**: By providing TypeConverter, any types can be stored.

## Download

Project build.gradle

```groovy
allprojects {
    repositories {
        ...
        maven { url "https://jitpack.io" }
    }
}
```

App build.gradle

```groovy
dependencies {
    ...
    annotationProcessor 'com.github.kobakei.spot:compiler:LATEST_VERSION'
    compile 'com.github.kobakei.spot:library:LATEST_VERSION'
}
```

`LATEST_VERSION` is  [![JitPack](https://jitpack.io/v/kobakei/spot.svg)](https://jitpack.io/#kobakei/spot)

NOTE: if you use Android Gradle Plugin before 2.2.0, you must use android-apt plugin instead of [annotationProcessor](https://bitbucket.org/hvisser/android-apt) configuration.

## Basic usage

Annotate your entity class with `@Pref` and fields with `@Pref***`.

```java
@Pref(name = "foo")
public class MyEntity {

  // Integer field
  // defaultValue can be omitted.
  @PrefInt(name = "hoge", defaultValue = 1)
  public int hoge;

  // String field
  // defaultValue can be omitted.
  @PrefString(name = "fuga", defaultValue = "Hello")
  public String fuga;

  // Default constructor is needed.
  public MyEntity() {}
}
```

Once your build, `MyEntitySpotRepository` class will be generated in same package as entity class.

```java
// Get
MyEntity entity = MyEntitySpotRepository.getEntity(context);

// Put
entity.hoge = 123;
entity.fuga = "Goodbye"
MyEntitySpotRepository.putEntity(context, entity);
```

## Field annotations

|Annotation|Type|
|---|---|
|`@PrefInt`|`int`|
|`@PrefLong`|`long`|
|`@PrefFloat`|`float`|
|`@PrefBoolean`|`boolean`|
|`@PrefString`|`String`|
|`@PrefStringSet`|`Set<String>`|

## Type converter

Spot supports the above 6 types as fields but other types can also be supported by providing `TypeConverter` class.

For example, let's support `java.util.Date` class as entity field.

At first, subclass of `TypeConverter` is needed. The follow sample gets/puts `Date` value from SharedPreferences by converting from/to `Long`.

```java
public class DateTypeConverter extends TypeConverter<Date, Long> {
    @Override
    public Date convertFromSupportedType(Long t) {
        return new Date(t);
    }

    @Override
    public Long convertToSupportedType(Date t) {
        return t.getTime();
    }
}
```

At last, annotate `Date` field with converter option.

```java
@Pref(name = "foo")
public class MyEntity {
    @PrefLong(name = "date", converter = DateTypeConverter.class)
    public Date date;
}
```

## Contribute this project

Small bug fixes are welcomed. If you want to add new feature, please raise issue.

## License

```
Copyright 2016 Keisuke Kobayashi

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
