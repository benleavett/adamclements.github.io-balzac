# Avoiding up-front load times in clojure

In my previous post I covered a problem I was having where core.async loaded namespaces which were entirely unnecessary at runtime (support libraries for macro transformations) and this was adversely affecting my load times. Since then I have probed further into the problem of getting my Clojure on Android app up and running effectively in sub 1s.

