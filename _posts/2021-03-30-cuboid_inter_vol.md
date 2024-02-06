---
layout: article
title: Cuboid 겹침 정도 계산하기
tags: Cuboid Python
aside:
  toc: true
---

업무 중 두 개의 Cuboid 사이의 겹치는 정도를 파악해야 하는 일이 생겨서 찾아본 내용 정리 👩🏻‍💻

사용 및 참고하면 좋을 것 같은 라이브러리 : [bbox](https://github.com/varunagrawal/bbox/blob/0d0d141fadf630ae079415d656fb9fb321a641eb/bbox/geometry.py#L150) <br>

- 이 경우에 3d Cuboid가 z축이 같은 레벨에 있고 yaw(z-axis)회전만 있는 경우에서만 사용할 수 있음
- [We follow the KITTI format and assume **only yaw rotations** (along z-axis)](https://github.com/varunagrawal/bbox/blob/master/bbox/metrics.py)
- [Note that I follow the convention from KITTI where I just assume that the boxes are on the **same z-axis level and can rotate around the z-axis.**](https://github.com/varunagrawal/bbox/issues/8)
- 비교하려고 하는 2개의 Cuboid의 바닥(Z축) 위치는 거의 동일한 선상이어야 함!


```python
# 라이브러리에서 아래의 방식을 통해 inter_vol을 구함
# 여기서 a와 b는 BBox3D로 라이브러리에 있는 class

# p = all corners of box in order
# 2D 박스 면적 구하는 부분 (Sutherland-Hodgman 알고리즘을 이용했다고 함)
intersection_points = polygon_intersection(a.p[0:4, 0:2], b.p[0:4, 0:2])
inter_area = polygon_area(intersection_points)

# cz = Bbox3D의 z축 center point
# h = Bbox3D의 height
# 1D의 겹치는 길이 구하는 부분
zmax = np.minimum(a.cz, b.cz)
zmin = np.maximum(a.cz - a.h, b.cz - b.h)

inter_vol = inter_area * np.maximum(0, zmax-zmin)
```

---
라이브러리에서 에서 3D 박스의 겹칩 정도 계산하는 [방법](https://stackoverflow.com/questions/41874851/numerically-calculate-intersecting-volume-of-two-cuboids) (위와 같이 한 축이 고정되어 있으며 그 축에 회전이 있는 경우)

- Bird's eye view로 박스를 보았을 때 2D 박스의 intersection 면적을 구하기

![image](https://github.com/2cong/2cong/assets/60612551/8c59ab1d-508d-4710-95f9-5d57a9a23080)

- 두개의 cuboid 박스의 높이 중 겹치는 길이를 구하기 (1D의 길이)
- 2D Box의 면적 x 겹치는 1D의 길이 → 겹쳐져 있는 박스의 volume
- 만약 필요한 경우 [Shapely로 2D면적 구하고 1D의 길이]( https://stackoverflow.com/questions/44797713/calculate-the-area-of-intersection-of-two-rotated-rectangles-in-python)를 구하면 될지?! 


---

#### 확인 1

##### 1. z축이 다른 선상인 경우

 위의 참고 로직에서 1D의 겹치는 길이 구하는 부분을 리서치 / 수정하면 위의 과정을 적용할 수 있어보임
 
 <img width="402" alt="image" src="https://github.com/2cong/2cong/assets/60612551/fd88a7ff-f14d-469e-b39b-44277d0ffa5b">
 
 양 선의 끝 점을 구하고 max의 min(여기서는 Y2), min의 max(X1) 위치를 구하기 → 두 점의 거리

##### 2. yaw로만 회전하지 않고 여러 축으로 회전이 있다면 위의 방법 사용 불가능 😭

---

#### 확인 2

##### cuboid의 좌표를 BBox3D class의 인스턴스로 만들 수 있을지?
위의 bbox 라이브러리 내에 정의된 함수를 사용하려면 내가 가지고 있는 cuboid의 좌표 및 정보를 이용하여 BBox3D class의 인스턴스를 생성 할 수 있어야 한다.
    
[Bbox3D 인스턴스](https://github.com/varunagrawal/bbox/blob/0d0d141fadf630ae079415d656fb9fb321a641eb/bbox/bbox3d.py)에 필요한 내용은 아래와 같다.

 - x (:py:class:`float`): X axis coordinate of 3D bounding box. Can be either center of bounding box or back-bottom-left corner.
 - y (:py:class:`float`): Y axis coordinate of 3D bounding box. Can be either center of bounding box or back-bottom-left corner.
 - z (:py:class:`float`): Z axis coordinate of 3D bounding box. Can be either center of bounding box or back-bottom-left corner.
 - length (:py:class:`float`, optional): The length of the box (default is 1).
 - width (:py:class:`float`, optional): The width of the box (default is 1).
 - height (:py:class:`float`, optional): The height of the box (default is 1).
 - rw (:py:class:`float`, optional): The real part of the rotation quaternion (default is 1).
 - rx (:py:class:`int`, optional): The first element of the quaternion vector (default is 0).
 - ry (:py:class:`int`, optional): The second element of the quaternion vector (default is 0).
 - rz (:py:class:`int`, optional): The third element of the quaternion vector (default is 0).
 - euler_angles (:py:class:`list` or :py:class:`ndarray` of float, optional): Sequence of euler angles in `[x, y, z]` rotation order (the default is None).
 - is_center (`bool`, optional): Flag to indicate if the provided coordinate is the center of the box (the default is True).

<br>
<br>

이 중 x, y, z, length, width, height 는 쉽게 구할 수 있었고 회전에 대한 정보를 표현 할 방법을 찾으면 된다. 나의 경우에는 euler_angles을 사용하여 회전을 표시할 수 있었다!  <br>
이 때 라이브러리에서 사용하는 회전 축과 내가 사용하는 회전 축의 범위가 다를 수 있기 때문에 이를 맞춰주어야 한다. <br>

라이브러리를 보니 euler_angles 값을 받으면 각도를 Quaternion 방식으로 변환해서 사용하고 있었다.

```python
if euler_angles:
  # we need to apply y, z and x rotations in order
  # http://www.euclideanspace.com/maths/geometry/rotations/euler/index.htm
	self._q = Quaternion(axis=[0, 1, 0], angle=euler_angles[1]) * \
  Quaternion(axis=[0, 0, 1], angle=euler_angles[2]) * \
  Quaternion(axis=[1, 0, 0], angle=euler_angles[0])
```

위에서 사용한 계산은 [pyquaternion](http://kieranwynn.github.io/pyquaternion/)의  Quaternion class 이다. 
Quaternion class에서 angle → radian 값이며 범위는  (-pi:pi) (-180 ~ 180) 로 되어있다. 따라서 이 값으로 맞춰주면 BBox3D class의 인스턴스를 생성 할 수 있다!

<br>
<br>