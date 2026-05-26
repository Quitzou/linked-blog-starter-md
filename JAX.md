
## Updating

in-place indexed updating
```
jax_array = jax_array.at[1, :].set(1.0)
jax_array = jax_array.at[::2, 3:].add(7.)
```

## Funktionen parallelisieren

```
from jax import jit	
norm_compiled = jit(norm)
```
requires all arrays to have static shape

## Ableitungen

```
import jax.numpy as jnp
from jax import grad

cos = grad(jnp.sin)
print(cos(0.))
```
can only produce one output value, can be combined with parallel 
```
from jax import jacobian
print(jacobian(jnp.exp)(x_small))
```
for automatic Jacobian calculation


## Quelle

https://docs.jax.dev/en/latest/notebooks/thinking_in_jax.html