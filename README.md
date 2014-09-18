BMKMapView-draw-line
====================

百度地图画线：根据自己现有的坐标点直接画路线，效率还是可以的，在这做一下标记

方法如下：

- //删除线路覆盖物
- -(void)deleteOverlays
  {
  NSArray *arrayOverlay = [[NSArray alloc] initWithArray:_navMapView.overlays];
	[_navMapView removeOverlays:arrayOverlay];
}

- //画自己的路线
- -(void)configureRoutesAtIndex:(NSInteger)index {
  [self deleteOverlays];
    
  //得到线路条数及详情
  NSArray *syRoteLineArr = [Common getScenicDayLineDataArrayWithCityID:self.scenic.city_id scenicID:self.scenic.ID];
    
  //得到坐标点 时间放在[0],之后是坐标点如：[1]：	@"0#113.144261_40.116341"
  NSArray *syScenicArr = [Common getSingleScenicArrayWithObject:syRoteLineArr[index]];
    
  //坐标点个数
  NSUInteger syRotePointCount = syScenicArr.count - 1;
    
  //我们自己指定的经纬度坐标点数组
  CLLocationCoordinate2D *syPointArr =  new CLLocationCoordinate2D[syRotePointCount];

  //添加经纬度到数组
  for (int k = 1; k < syRotePointCount + 1; k ++)
  {
    //得到经纬度和前面的下标
    NSArray *syViewpointDeatilArr = [syScenicArr[k] componentsSeparatedByString:@"#"];
        
    SYLog(@"viewpoint--- %@",syViewpointDeatilArr[1]);

    //得到经纬度
    NSArray *syViewpointsArr = [syViewpointDeatilArr[1] componentsSeparatedByString:@"_"];

    //添加坐标数据
    CLLocationCoordinate2D pointCoor = (CLLocationCoordinate2D){[syViewpointsArr[1] doubleValue],[syViewpointsArr[0] doubleValue]};
        
    syPointArr[k - 1] = pointCoor;
  }

  //开始画线
  _routeLine = [BMKPolyline polylineWithCoordinates:syPointArr count:syRotePointCount];
  
  [self hideSYHUDAnimation:NO];
    
	if (_routeLine != nil)
  {
		[_navMapView addOverlay:_routeLine];
	}
  else
  {
    [[SAlertTostView defaultTost] showTostWithFailMsg:kVTERRORINFO_GPS_ERROR_FAIL];
        
    SYLog(@"search failed!");
  }
    
  [_navMapView setZoomLevel:self.scenic.zoom];
  [_navMapView setCenterCoordinate:centerCoordinate animated:YES];
    
  delete []syPointArr;
}

