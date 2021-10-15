### reg2d3d project set-up

#### The main idea

- Register 3 poses: pelvis, left fenur and right femur to the Siemaens CIOS C-arm images with timely-expensive methods
- Use the registered poses to calculate the 3d landmarks/3d segmentations in the fluroscopic view
- Train a U-Net with the fluroscopic view as input and the 2d landmarks and 2d segmentations as labels

#### Dependencies

Setup pytorch first.

```
pip install h5py
pip install SimpleITK
pip install pandas
```

And optionally:

```
pip install jupyterlab
```

#### Training

```
python -u train.py ../data/ipcai_2020_ds_8x.h5 --train-pats 2,3,4,5,6 --num-classes 7 --init-lr 0.1 --momentum 0.9 --unet-batch-norm --unet-no-max-pool --unet-img-dim 192 --unet-num-lvls 6 --batch-size 5 --max-num-epochs 500 --unet-init-feats-exp 5 --wgt-decay 0.0001 --data-aug --unet-padding --nesterov --checkpoint-net yy_check_net.pt --checkpoint-freq 100 --use-lands --best-net yy_best_net.pt --lr-sched plateau --train-valid-split 0.85 --train-loss-txt yy_train_loss.txt --valid-loss-txt yy_valid_loss.txt --heat-coeff 0.5
```

#### Inference

```
python -u test_ensemble.py ../data/ipcai_2020_ds_8x.h5 spec_1_test.h5 --pats 1 --nets yy_best_net.pt
```

```
python est_lands_csv.py spec_1_test.h5 nn-heats --use-seg nn-segs --pat 1 --out spec_1_lands.csv
```

```
python overlay_est_ann.py ../data/ipcai_2020_ds_8x.h5 spec_1_test.h5 nn-segs 1 3 spec_1_est_ann_proj_3.png --lands --no-gt-lands --lands-csv spec_1_lands.csv
```

```
python compute_actual_dice_on_test.py ../data/ipcai_2020_ds_8x.h5 spec_1_test.h5 nn-segs spec_1_dice.csv 1
```

```
python overlay_est_heat.py ../data/ipcai_2020_ds_8x.h5 spec_1_test.h5 nn-heats 1 3 1 spec_1_proj_3_fhr_est_heat.png
```

```
python overlay_est_heat.py ../data/ipcai_2020_ds_8x.h5 spec_1_test.h5 nn-heats 1 3 0 spec_1_proj_3_fhl_est_heat.png

```
python extract_seg_from_nn_h5.py spec_1_test.h5 96 proj_96_est_seg.png