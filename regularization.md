- Inf or Nan in Gradient

  - ```python
    torch.nn.utils.clip_grad_norm(model.parameters(), max_norm=20)
    grad = optimizer.param_groups[0]['params'][0].grad
    torch.norm(grad)
    ```

- Flooding

  - 控制training loss在一个较小的值附近波动
  - [Do We Need Zero Training Loss A￿er Achieving Zero Training Error?](https://arxiv.org/abs/2002.08709)

  - `loss = (loss - b).abs() + b`

- DropBlock

  - 卷积层的特征图中相邻位置元素在空间上共享语义信息，Dropout在整幅特征图内随机丢弃元素，但与其相邻的元素依然可以保有该位置的语义信息。为了更加有效地丢弃局部语义信息，激励网络学到更加鲁邦有效的特征，应该按块丢弃，也就是DropBlock
  - [DropBlock: A regularization method for convolutional networks](https://arxiv.org/abs/1810.12890)
  - [GitHub](https://github.com/miguelvr/dropblock)

- shake-shake
  - 