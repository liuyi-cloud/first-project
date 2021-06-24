% [Z,A]=MedianAndShift(LR, D, HRsize, Dres)
%
% Estimates (Robustly) the blurred high resolution as a median of the LR
% images after upsampleing and shifting to correct position. This is
% prooved by Farisu et al in "Fast and Robust Multiframe Super Resolution"
% to be the estimator (with no regularization) which minimizes the L1
% norm. 
%
% An efficient implementation is performed here in which we note that the
% high resolution image consists of the median of all the LR images which
% have the same displacement value. That is, the LR images are partitioned(分割)
% to r^2 unique groups, each group have equal displacement values in the HR
% image. The median of each group is then upsampled and shifted into the HR
% image. 
% 
% In some cases, not all r^2 displacements exists. This leaves us with
% undetermined cases. In these case some other interpolatoin method needs
% to be implemented. In this implementation we "fill" the holes using
% a spatial median filter.
%
% Inputs:
%
% LR - The sequence of low resolution images
% D  - The displacement vector for each frame
% HRsize - The size of the HR image
% Dres - The resolution scale factor.
function [Z,A]=MedianAndShift(LR, D, HRsize, Dres)

% Allocate high resolution image
Z = zeros(HRsize);
A = ones(HRsize);

S = zeros(Dres);

% Loop on each possible displacement value (should be much less than the
% number of images in LR for over determined solution)
for x=Dres:2*Dres-1
  for y=Dres:2*Dres-1
    
    I = D(:,1)==x & D(:,2)==y;%判断位移矩阵值，生成逻辑数组
    len = length(find(I==true));%确定I矩阵等于1的长度
    
    % Handle only cases in which there is at least one LR at this
    % displacement
    if len>0

      % Indicate data exists for this shift
      S(x-Dres+1, y-Dres+1)=1;

      Z(y:Dres:size(Z, 1),x:Dres:size(Z, 2))=median(LR(:,:,I), 3);
      %median(M,dim),dim为1，2。其中1表示按每列返回一个值,为该列从大到小排列的中间值, 
      %2表示按每行返回一个值,为该行从大到小排列的中间值
      %3表示第三维度 I表示逻辑矩阵为正确的位置取值  此次为取8副影像的中间值
      A(y:Dres:size(Z, 1),x:Dres:size(Z, 2))=len;
      % A矩阵储存几个像素的中间值
    end

  end
end

% Find under-determined shifts
[X,Y] = find(S==0);
% 找到没有进行取中值的漏洞处
if ~isempty(X)
  
  % Compute a median filter with window size of the resolution factor
  % assuming that more than 50% of the shifts should be determined 
  Zmed=medfilt2(Z, [Dres Dres]);
  %中值滤波 [Dres Dres]为窗口大小
  
  % Loop on each hole and fill with median
  for i=1:length(X)
    x =X(i)+Dres-1;
    y =Y(i)+Dres-1;

    Z(y:Dres:size(Z, 1),x:Dres:size(Z, 2))=Zmed(y:Dres:size(Z, 1),x:Dres:size(Z, 2));
    
  end
  
end
%   
%   % Compute 
%   
%   % Interpolate the holes with the weighted average of the determined pixels
%   [Xd,Yd] = find(S);
%   
%   % Loop on each hole
%   for i=1:length(X)
%     
%     % Compute a factor based on distance from each determined pixel to the hole
%     Alpha=0.7.^(abs(Xd-X(i))+abs(Yd-Y(i)));
%     % Normalize alpha
%     Alpha = Alpha./sum(Alpha);
%     
%     % Loop on each shift and set the factor value for it
%     for j=1:length(Xd)
%       
%       x =X(i)+Dres-1;
%       y =Y(i)+Dres-1;
%       xd=Xd(j)+Dres-1;
%       yd=Yd(j)+Dres-1;
%       
%       Z(y:Dres:size(Z, 1),x:Dres:size(Z, 2))=...
%         Z(y:Dres:size(Z, 1),x:Dres:size(Z, 2))+...
%         Z(yd:Dres:size(Z, 1),xd:Dres:size(Z, 2)).*Alpha(j);
%       
%     end
%     
%   end
%   
% end

A = sqrt(A);
