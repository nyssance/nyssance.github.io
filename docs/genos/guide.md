# Guide

> Show you the code.

## __Step 0 (About 10 minutes to 1 day):__

Install [Android Studio](https://developer.android.com/studio).

## __Step 1 (2 minutes):__

Create New project. [official guide][1]

|Screen | Configure|
|------ | ---------|
|Select a Project Template | Empty Activity|
|Configure Your Project | Minimum SDK: API 27|

Open the _build.gradle (Module: app)_ file for your app module, add Genos to the `dependencies` section.

<!-- tabs:start -->

<!-- tab:Groovy -->

```groovy
dependencies {
    ...
    implementation 'com.nyssance.genos:genos:2.0.0'
    ...
```

<!-- tab:Kotlin -->

```kotlin
dependencies {
    ...
    implementation("com.nyssance.genos:genos:2.2.0")
    ...
```

<!-- tabs:end -->

## __Step 2 (5 minutes):__

Create 4 classes: _User_, _APIService_, _Global_, _UserList_

_User_

```kotlin
import com.google.gson.annotations.SerializedName

data class User(
        val id: Long,
        @SerializedName("login") val username: String,
        @SerializedName("avatar_url") val avatarUrl: String
)
```

_APIService_

```kotlin
interface APIService {
    @GET("repos/square/retrofit/contributors")
    fun userList(@Query("page") page: Int): Call<List<User>>

    @GET("users/{username}")
    fun userDetail(@Path("username") username: String): Call<User>
}
```

_Global_

```kotlin
import genos.Global

lateinit var API: APIService

fun config() {
    ...
    API = Global.retrofit("https://api.github.com").create(APIService::class.java)
}
```

_UserList_

```kotlin
import genos.extension.setImage
import genos.ui.fragment.generic.List
import genos.ui.viewholder.DefaultHolder

class UserList : List<User, DefaultHolder>() {
    override fun onCreate() {
        call = API.userList(page)  // A retrofit call of this fragment.
        tileId = R.layout.list_item_subtitle  // The layout res id of list item.
    }

    override fun onDisplayItem(item: User, view: DefaultHolder, viewType: Int) {
        with(view) {
            icon?.setImage(item.avatarUrl)
            title?.text = item.username
            subtitle?.text = item.id.toString()
        }
    }

    override fun onOpenItem(item: User) {
        Snackbar.make(listView, "Replace with your own action", Snackbar.LENGTH_SHORT).show()
    }
}
```

## __Step 3 (1 minutes)__

Modify _MainActivity_, _AndroidManifest.xml_

```kotlin
import genos.ui.activity.TabBarActivity
import genos.ui.fragment.PlaceholderFragment

class MainActivity : TabBarActivity(1) {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        config()
        supportActionBar?.setTitle(R.string.app_name)
        with(fragments) {
            append(R.id.navigation_home, UserList())
            append(R.id.navigation_discover, PlaceholderFragment.instance("2"))
            append(R.id.navigation_me, PlaceholderFragment.instance("3"))
        }
    }
}
```

_AndroidManifest.xml_

```xml
    <uses-permission android:name="android.permission.INTERNET" />

    <application
        ...
        android:theme="@style/Theme.Genos">
        ...
    </application>

</manifest>
```

## __Step 4__

Run it  
__Congratulations! your are an Android expert~~__

[1]: https://developer.android.com/studio/projects/create-project
