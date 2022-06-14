# Creating a Basic Config

Quilt Loader has a config system built-in that is based on the [Quilt Config](https://github.com/QuiltMC/quilt-config)
library. It makes it easy to serialize, deserialize, and access config values for your mod. Creating a basic config
file is easy.

### Supported Types

Quilt Config has support for fields of the following types by default:

 * Basic types (`int`, `long`, `float`, `double`, `boolean`, `String`, or any enum)
 * Complex types [`org.quiltmc.config.api.values.ValueList`](https://github.com/QuiltMC/quilt-config/blob/master/src/main/java/org/quiltmc/config/api/values/ValueList.java),
   [`org.quiltmc.config.api.values.ValueMap`](https://github.com/QuiltMC/quilt-config/blob/master/src/main/java/org/quiltmc/config/api/values/ValueMap.java),
   or some [`org.quiltmc.config.api.values.ConfigSerializableObject`](https://github.com/QuiltMC/quilt-config/blob/master/src/main/java/org/quiltmc/config/api/values/ConfigSerializableObject.java)
   
> Note: There will be a module in the future that implements `ConfigSerializableObject` on a number of built-in
> Minecraft classes. For now, it's only useful if you have your own class that you want to represent in configs. 

### Creating Your Config
Start by creating a class to represent your config file. The class should extend
[`org.quiltmc.config.api.WrappedConfig`](https://github.com/QuiltMC/quilt-config/blob/master/src/main/java/org/quiltmc/config/api/WrappedConfig.java):

```java
public class MyConfig extends WrappedConfig {
    // Fields must be final
    public final int volume = 0; // This will be the default value
    public final String name = "Haven";
    
    // The @Comment annotation can be used to add comments to your config file
    @Comment("The number of diamonds dropped by dirt blocks")
    public final int numberOfDiamonds = 10;
    
    // @FloatRange can be used to apply limits to float and double fields,
    // while @IntegerRange does the same for ints and longs
    @FloatRange(min = -10D, max = 10D)
    public final float someValue = 0D;
    
    // @Matches can be used to ensure that String fields satisfy a given regex
    @Matches("[a-zA-Z ]+")
    public final String alphabeticValue = "The quick brown fox jumped over the lazy dog";
    
    // Ranges and @Matches can also be applied to the values of a ValueMap or ValueList
    @IntegerRange(min = 0, max = Long.MAX_VALUE)
    public final ValueMap<Integer> nutritionValues = ValueMap.builder(0)
            .put("cookie", 10).put("porkchop", 100).put("mango", 30)
            .build();
}
```

Now that we have a class representing all the information we want to be configurable, we can get an instance of our
config object by registering it via the [`org.quiltmc.loader.api.config.QuiltConfig`](https://github.com/QuiltMC/quilt-loader/blob/develop/src/main/java/org/quiltmc/loader/api/config/QuiltConfig.java)
class:

```java
public class MyMod implements ModInitializer {
    public static final MyConfig CONFIG = QuiltConfig.create(
            "my-awesome-mod",   // The family id, this should usually just be your mod ID
            "config",           // The id for this particular config, since your mod might have multiple
            MyConfig.class      // The config class you created earlier
    );
    
    @Override
    public void onInitialize(ModContainer mod) {
        System.out.println(CONFIG.alphabeticValue); // Printing the value we created earlier
    }
}
```