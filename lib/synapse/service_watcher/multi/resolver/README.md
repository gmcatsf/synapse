# Resolvers

A resolver decides how to combine, or resolve, multiple service watchers into a
single result. That is, it operates as a `reducer` function to allow more than
one service watchers to appear, and act, as a single watcher.

The stub methods listed below should be overridden by any children classes.
If any additional methods are overridden (such as `initialize`), be sure to
call `super` first.

```ruby
require "synapse/service_watcher/multi/resolver/base"

class Synapse::ServiceWatcher::MultiWatcher::Resolver
   class MyResolver < BaseResolver
	  def validate_opts
	     # validate options in @opts and optionally, wathchers in @watchers
	  end

      def start
	     # start resolver
		 # if you need to trigger a reconfigure, call send_notification
	  end

	  def stop
	     # stop resolver
	  end

	  def merged_backends
	     # return a single list of backends
	  end

	  def merged_config_for_generator
	     # return a single hash for generator config
	  end

	  def healthy?
	     # return whether or not the watchers are healthy
	  end
   end
end
```

### Resolver Plugin Interface
Synapse deduces both the class path and class name from the `method` key within
the resolver configuration.  Every resolver is passed configuration with the
`method` key, e.g. `base` or `s3_toggle`.

#### Class Location
Synapse expects to find your class at `synapse/service_watcher/multi/#{method}`.
You must make your resolver available at that path, and Synapse can "just work" and
find it.

#### Class Name
These method strings are then transformed into class names via the following
function:

```
method_class  = method.split('_').map{|x| x.capitalize}.join.concat('Resolver')
```

This has the effect of taking the method, splitting on '_', capitalizing each
part and recombining with an added 'Resolver' on the end. So `fallback`
becomes `FallbackResolver`, and `s3_toggle` becomes `S3ToggleResolver`. Make
sure your class name is correct.
