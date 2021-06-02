```
  $temp=false;
        $hrResume = HrResume::filter($this->request->all(), ResumeFilter::class)
            ->paginate($this->request->input('pageSize'));
        if ($export != null) {
            $inputAll=$this->request->except(['page','pageSize']);//得到除却页码与每页显示的个数的全部输入项
//            $inputAll=array_values($inputAll);
            $inputAll=array_filter($inputAll)==null;

            dd($inputAll);
//            dd(
//                $this->request->input('phone') == null &&
//                $this->request->input('realname') == null &&
//                $this->request->input('role_text') == null &&
//                $this->request->input('jobStatus') == null &&
//                $this->request->input('job_city') == null &&
//                $this->request->input('advice_is_replied') == null &&
//                $this->request->input('gender') == null &&
//                $this->request->input('job_industry') == null &&
//                $this->request->input('grade') == null &&
//                $this->request->input('city') == null
//            );
            //定义时间
            if ($inputAll==true) {
                $hrResume = HrResume::all();
            }
            $date = Carbon::now()->toDateTimeString();
            $hrResume = $this->resume->serializeExcelResume($hrResume);
            return Excel::download(new ResumeExport($hrResume), $date . '简历信息.xlsx');
```