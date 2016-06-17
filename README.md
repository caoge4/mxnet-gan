# MXNet GAN

MXNet module implementation of multi GPU compatible generative models.

## Usage

```python
from mxgan import module, generator, encoder, viz

context = [mx.gpu()]

gmod = module.GANModule(
    symbol_generator=sym_gen,
    symbol_encoder=sym_dec,
    context=context,
    data_shape=data_shape,
    code_shape=rand_shape)

gmod.init_params(mx.init.Xavier(factor_type="in", magnitude=2.34))
gmod.init_optimizer(
    optimizer="adam",
    optimizer_params={
        "learning_rate": lr,
  })

metric_acc = mx.metric.CustomMetric(ferr)

for epoch in range(num_epoch):
    train.reset()
    metric_acc.reset()
    for t, batch in enumerate(train):
        # GAN training
        gmod.update(batch)
        # update metrics
        gmod.temp_label[:] = 0.0
        metric_acc.update([gmod.temp_label], gmod.outputs_fake)
        gmod.temp_label[:] = 1.0
        metric_acc.update([gmod.temp_label], gmod.outputs_real)
        # visualize
        if t % 100 == 0:
            logging.info("epoch: %d, iter %d, metric=%s", epoch, t, metric_acc.get())
            viz.imshow("gout", gmod.temp_outG[0].asnumpy(), 2)
            diff = gmod.temp_diffD[0].asnumpy()
            diff = (diff - diff.mean()) / diff.std() + 0.5
            viz.imshow("diff", diff)
            viz.imshow("data", batch.data[0].asnumpy(), 2)
```