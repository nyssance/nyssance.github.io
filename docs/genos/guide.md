# Guide

> Show you the code.

## Step 0 (About 10 minutes to 1 day):

Install [Android Studio](https://developer.android.com/studio).

## Step 1 (2 minutes):

Create New project. [official guide][1]

|Screen | Configure|
|------ | ---------|
|Select a Project Template | Empty Activity|
|Configure Your Project | Minimum SDK: API 27|

Open the **build.gradle** (Module: app) file for your app module, add Genos to the `dependencies` section.

<!-- tabs:start -->

<!-- tab:Groovy -->

```groovy
dependencies {
    implementation 'com.nyssance.genos:genos:2.0.0'
    ...
}
```

<!-- tab:Kotlin -->

```kotlin
dependencies {
    implementation("com.nyssance.genos:genos:2.2.0")
    ...
}
```

<!-- tabs:end -->

## Step 2 (5 minutes):

Create 3 classes.

> **User**

```kotlin
import com.google.gson.annotations.SerializedName

data class User(
    val id: Long,
    @SerializedName("login") val username: String,
    @SerializedName("avatar_url") val avatarUrl: String
)
```

> **Api**

```kotlin
import genos.vendor.retrofit
import retrofit2.Call
import retrofit2.http.GET
import retrofit2.http.Query

const val BASE_URL = "https://api.github.com"
val API: Api = retrofit(BASE_URL).create(Api::class.java)

interface Api {
    @GET("repos/square/retrofit/contributors")
    fun userList(@Query("page") page: Int): Call<List<User>>
}
```

> **UserList**

```kotlin
import genos.extension.setImage
import genos.ui.fragment.generic.List
import genos.ui.viewholder.Holder

class UserList : List<User, Holder>() {
    override fun onCreate() {
        call = API.userList(page)  // A retrofit call of this fragment.
        tileId = R.layout.list_item_subtitle  // The layout res id of list item.
    }

    override fun onDisplayItem(item: User, viewHolder: Holder, viewType: Int) {
        with(viewHolder) {
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

## Step 3 (1 minutes)

Modify **MainActivity**.

```kotlin
import genos.ui.activity.TabBarActivity
import genos.ui.fragment.PlaceholderFragment

class MainActivity : TabBarActivity(mapOf(
    R.id.navigation_1 to UserList(),
    R.id.navigation_2 to PlaceholderFragment.instance(2),
    R.id.navigation_3 to PlaceholderFragment.instance(3)
)) {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        supportActionBar?.setTitle(R.string.app_name)
    }
}
```

## Step 4

Run it  
**Congratulations! your are an Android expert~~**

[1]: https://developer.android.com/studio/projects/create-project
